# Transaction

A Transaction is how bitcoins are transferred on the blockchain.
It comprises of a set of [Transaction Inputs](#transaction-input) which will be spent to a set of [Transaction Outputs](#transaction-output).
The mining nodes and full node software ensures that every transaction follows the blockchain's rules before admitting a transaction into a block.

Verification of a transaction ensures that:
- The Transaction Inputs have not already been spent.
- The total value contained in the Transaction Inputs is greater than (or equal) to the value specified within the Transaction Outputs.
- The Transaction is syntactically and cryptographically valid, and that the previous Unspent Transaction Outputs' [Locking Scripts](/protocol/blockchain/transaction/locking-script) are correctly unlocked via the Transaction Inputs' [Unlocking Script](/protocol/blockchain/transaction/unlocking-script).

## Format

| Field | Length | Format | Description |
|--|--|--|--|
| version | 4 bytes | unsigned integer<sup>[(LE)](/protocol/misc/endian/little)</sup> | The version of the transaction format.  Currently `0x02000000`.<br>For more details refer to the [transaction version history](/history/transaction-version). |
| input count | variable | [variable length integer](/protocol/formats/variable-length-integer) | The number of inputs in the transaction. |
| transaction inputs | variable | `input_count` [transaction inputs](#transaction-input) | Each of the transaction's inputs serialized in order. |
| output count | variable | [variable length integer](/protocol/formats/variable-length-integer) | The number of output in the transaction. |
| transaction outputs | variable | `output_count` [transaction outputs](#transaction-output) | Each of the transaction's outputs serialized in order. |
| lock-time | 4 bytes | unsigned integer<sup>[(LE)](/protocol/misc/endian/little)</sup> | The block height or timestamp after which this transaction is allowed to be included in a block.  If less than `500,000,000`, this is interpreted as a block height.  If equal or greater than `500,000,000`, this is interpreted as a unix timestamp in seconds. Ignored if all of the transaction input sequence numbers are `0xFFFFFFFF`.<br/><br/>Note that at 10 minutes per block, it will take over 9,500 years to reach block height 500,000,000.  Also note that when Bitcoin was created the unix timestamp was well over 1,000,000,000.<br/><br/>Additionally, since [BIP-113](/protocol/forks/bip-0113), when the lock-time is intepreted as a time, it is compared to the [median-time-past](#median-time-past) of a block, not it's timestamp. |

### Median-Time-Past

The median-time-past, or MTP, of a block is defined as the median block timestamp of the 11 blocks preceding a block.
This is used in calculations to avoid circumstances where consecutive blocks may not have strictly increasing timestamps.

Note, however, that this means that transactions utilizing time-based locking will not be included in a block immediately after their lock-time is reached.
Instead, there will need to be 6 blocks after the lock-time in order for the MTP to indicate that the lock-time has been reached.
This means that such transactions will experience an additional delay of an hour on average.

## Transaction Input

Transaction inputs are the "debits" of Bitcoin Cash and not only designate the satoshis that will be transferred as a part of the transaction, but they also provide proof of ownership via the [Unlocking Script](/protocol/blockchain/transaction/unlocking-script).
A Transaction Input references an unspent Transaction Output (oftern referred to as a "UTXO"), from a prior transaction.
If a Transaction would like to spend multiple UTXOs, it must have multiple inputs, one for each UTXO.
The Transaction Output that is being spent by a Transaction Input is often referred to as the "PrevOut", short for the "Previous Output".

### Format

| Field | Length | Format | Description |
|--|--|--|--|
| previous output transaction hash | 32 bytes | [transaction hash](/protocol/blockchain/hash) | The hash of the transaction containing the output to be spent. |
| output index | 4 bytes | unsigned integer<sup>[(LE)](/protocol/misc/endian/little)</sup> | The zero-based index of the output to spent in the previous output's transaction. |
| unlocking script length | variable | [variable length integer](/protocol/formats/variable-length-integer) | The size of the unlocking script in bytes. |
| unlocking script | variable | bytes<sup>[(BE)](/protocol/misc/endian/big)</sup> | The contents of the unlocking script. |
| sequence number | 4 bytes | unsigned integer<sup>[(LE)](/protocol/misc/endian/little)</sup> | As of [BIP-68](/protocol/forks/bip-0068), the sequence number is interpreted as a [relative lock-time](#relative-lock-time) for the input. |

### Relative Lock-Time Format

Since BIP-68, a transaction input's sequence number may be interpreted as a relative lock-time for the input.
That is, it prevents the transaction from being mined until a certain amount of time has past (or number of blocks mined) since the transaction containing the output to be spent was mined.
The 4 bytes of the sequence number are read from the input and interpreted as an unsigned integer.
The lowest-order bit is denotes as the 0th bit and the highest order bit as the 31st.
The following rules are used to interpret the value:

 - If bit 31 is set, there is no relative lock-time and the sequence number can be ignore for these purposes.
 - If bit 22 is set, the relative lock-time is interpreted as a number of 512-second intervals.
 - If bit 22 is not set, the relative lock-time is interpreted as a number of blocks.
 - Bits 15 through 0 are interpreted as a 16-bit unsigned integer which specify the relative lock-time quantity.

As with lock-time, when the relative lock-time is interpreted as a time, it is compared to the [median-time-past](#median-time-past) of a block, not it's timestamp.

## Transaction Output

Transaction outputs are the "credits" of Bitcoin Cash.
Each transaction output denotes a number of satoshis and the requirements for spending them.
These requirements take the form of a [locking script](/protocol/blockchain/transaction/locking-script) and can equate to anything from the satoshis only being spendable by the owner of a specific private key, to anyone being able to spend them, to no one being able to spend them.
While a transaction output has not been spent by another transaction (i.e. had its locking script "unlocked" by an input of a valid transaction) it is referred to as an unspent transaction output, or UTXO.
A Transaction Output that is being spent by a Transaction Input is often referred to as the "PrevOut", short for the "Previous Output".
In addition to number of satoshis, transaction outputs may optionally encode token state using the [token output format](#token-output-format) enabled in [HF-20230515](/protocol/forks/hf-20230515).

### Format

| Field | Length | Format | Description |
|--|--|--|--|
| value | 8 bytes | unsigned integer<sup>[(LE)](/protocol/misc/endian/little)</sup> | The number of satoshis to be transferred. |
| locking script length | variable | [variable length integer](/protocol/formats/variable-length-integer) | The size of the locking script in bytes. |
| locking script | variable | bytes<sup>[(BE)](/protocol/misc/endian/big)</sup> | The contents of the locking script. |

#### Token Output Format

Tokens may be encoded in outputs in addition to satoshi value using a "token prefix", a data structure that can encode a token category, zero or one non-fungible token (NFT), and an amount of fungible tokens (FTs).

For backwards-compatibility with existing transaction decoding implementations, a transaction output's token prefix (if present) is encoded before byte 0 of its locking bytecode, and the locking script length preceding the two fields is increased to cover both fields (such that the length could be renamed to "token prefix and locking script length").
The token prefix is not part of the locking bytecode and must not be included in bytecode evaluation.
If the output contains a token, then the serialized output format becomes:

| Field | Length | Format | Description |
|--|--|--|--|
| value | 8 bytes | unsigned integer<sup>[(LE)](/protocol/misc/endian/little)</sup> | The number of satoshis to be transferred. |
| token prefix and locking script length | variable | [variable length integer](/protocol/formats/variable-length-integer) | The combined size of full token prefix and the locking script in bytes. |
| PREFIX_TOKEN | 1 byte | constant | Magic byte defined at codepoint 0xef (239) and indicates the presence of a token prefix. |
| token category ID | 32 bytes | bytes | After the PREFIX_TOKEN byte, a 32-byte "token category ID" is required, encoded in OP_HASH256 byte order. |
| token bitfield | 1 byte | bitfield | A bitfield encoding two 4-bit fields is required. |
| \[NFT commitment length\] | variable | [variable length integer](/protocol/formats/variable-length-integer) | The size of the NFT commitment in bytes. Present only if token bitfield bit 0x40 is set. |
| \[NFT commitment\] | variable | bytes | The contents of the NFT commitment. Present only if token bitfield bit 0x40 is set. |
| \[FT amount\] | variable | [variable length integer](/protocol/formats/variable-length-integer) | An amount of fungible tokens, present only if token bitfield bit `0x10` is set. |
| locking script | variable | bytes<sup>[(BE)](/protocol/misc/endian/big)</sup> | The contents of the locking script. |

**Token Bitfield**

The token bitfield encodes part of the NFT state, and bit flags that specify whether optional fields are present.
The two 4-bit fields are specified as:
   1. `prefix_structure` (`token_bitfield & 0xf0`) - 4 bitflags, defined at the higher half of the bitfield, indicating the structure of the token prefix:
      1. `0x80` (`0b10000000`) - `RESERVED_BIT`, must be unset.
      2. `0x40` (`0b01000000`) - `HAS_COMMITMENT_LENGTH`, the prefix encodes a commitment length and commitment.
      3. `0x20` (`0b00100000`) - `HAS_NFT`, the prefix encodes a non-fungible token.
      4. `0x10` (`0b00010000`) - `HAS_AMOUNT`, the prefix encodes an amount of fungible tokens.
   2. `nft_capability` (`token_bitfield & 0x0f`) – A 4-bit value, defined at the lower half of the bitfield, indicating the non-fungible token capability, if present.
      1. If not `HAS_NFT`: must be `0x00`.
      2. If `HAS_NFT`:
         1. `0x00` – No capability – the encoded non-fungible token is an **immutable token**.
         2. `0x01` – The **`mutable` capability** – the encoded non-fungible token is a **mutable token**.
         3. `0x02` – The **`minting` capability** – the encoded non-fungible token is a **minting token**.
         4. Values greater than `0x02` are reserved and must not be used.

## Transaction Fee

Extra satoshis from the Transaction Inputs that are not accounted for in the Transaction Outputs may be collected by the miner as the transaction fee.

## Implicit Burning of Tokens

Extra satoshis from the Transaction Inputs that are not accounted for in the Transaction Outputs will be burned in such a transaction.
