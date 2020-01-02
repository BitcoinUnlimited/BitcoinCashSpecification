# Transaction

A Transaction is how bitcoins are transferred on the blockchain.
It comprises of a set of [Transaction Inputs](#transaction-inputs) which will be spent to a set of [Transaction Outputs](#transaction-outputs).
The mining nodes and full node software ensures that every transaction follows the blockchain's rules before admitting a transaction into a block.

Verification of a transaction ensures that:
- The Transaction Inputs have not already been spent.
- The total value contained in the Transaction Inputs is greater than (or equal) to the value specified within the Transaction Outputs.
- The Transaction is syntactically and cryptographically valid, and that the previous Unspent Transaction Outputs' [Locking Scripts](/protocol/blockchain/transaction/locking-script) are correctly unlocked via the Transaction Inputs' [Unlocking Script](/protocol/blockchain/transaction/unlocking-script).

## Format

| Field | Length | Format | Description |
|--|--|--|--|
| version | 4 bytes | unsigned integer<sup>[(LE)](/protocol/misc/endian/little)</sup> | The version of the transaction format.  Currently `0x02000000`. |
| input count | variable | [variable length integer](/protocol/formats/variable-length-integer) | The number of inputs in the transaction. |
| transaction inputs | variable | `input count` [transaction inputs](#transaction-inputs) | Each of the transaction's inputs serialized in order. |
| output count | variable | [variable length integer](/protocol/formats/variable-length-integer) | The number of output in the transaction. |
| transaction outputs | variable | `output count` [transaction outputs](#transaction-outputs) | Each of the transaction's outputs serialized in order. |
| lock time | 4 bytes | unsigned integer<sup>[(LE)](/protocol/misc/endian/little)</sup> | The block height or timestamp after which this transaction is allowed to be included in a block.  If less than `500,000,000`, this is interpreted as a block height.  If more than `500,000,000`, this is interpreted as a unix timestamp in seconds.  Ignored if all of the transaction input sequence numbers are `0xFFFFFFFF`.<br/><br/>Note that at 10 minutes per block, it will take over 9,500 years to reach block height 500,000,000.  Also note that when Bitcoin was created the unix timestamp was well over 1,000,000,000. |

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

## Transaction Output

Transaction outputs are the "credits" of Bitcoin Cash.
Each transaction output denotes a number of satoshis and the requirements are spending them.
These requirements take the form of a [locking script](/protocol/blockchain/transaction/locking-script) and can equate to anything from the satoshis only being spendable by the owner of a specific private key, to anyone being able to spend them, to no one being able to spend them.
While a transaction output has not been spent by another transaction (i.e. had its locking script "unlocked" by an input of a valid transaction) it is referred to as an unspent transaction output, or UTXO.
A Transaction Output that is being spent by a Transaction Input is often referred to as the "PrevOut", short for the "Previous Output".

### Format

| Field | Length | Format | Description |
|--|--|--|--|
| value | 8 bytes | unsigned integer<sup>[(LE)](/protocol/misc/endian/little)</sup> | The number of satoshis to be transferred. |
| locking script length | variable | [variable length integer](/protocol/formats/variable-length-integer) | The size of the unlocking script in bytes. |
| locking script | variable | bytes<sup>[(BE)](/protocol/misc/endian/big)</sup> | The contents of the locking script. |

## Transaction Fee

Extra satoshis from the Transaction Inputs that are not accounted for in the Transaction Outputs may be collected by the miner as the transaction fee.