
# Transaction Signatures

Transaction signatures are central to how [Bitcoin Cash transactions](/protocol/blockchain/transaction) are generally secured, preventing people other than the intended recipient of funds from spending them.  Bitcoin Cash signatures are created using [asymmetric cryptography](https://en.wikipedia.org/wiki/Public-key_cryptography) and involve generating a [hash](/protocol/blockchain/hash) of the transaction and performing a signature operation using the sender's private key.  Anyone with the corresponding public key can then verify the validity of the signature.  As described in [Standard Scripts](/protocol/blockchain/transaction/locking-script#standard-scripts), the [OP_CHECKSIG and related operations](/protocol/blockchain/script#cryptography) are used to validate signatures included in the unlocking script of a future transaction input.

However, there are a number of issues with signing a transaction that must be addressed:

  1. Transactions are identified by hashes of the full contents of the transaction
  2. The signatures are a part of the transaction data
  3. The signatures are created from a hash of the transaction's data

Points (1) and (2) mean that if the signature is changed, the transaction's hash will change.  Points (2) and (3) mean that the data that the signature hash preimage (i.e. the data that is hashed and signed) must not be the full transaction data. In addition, because signatures relate only to a single input to a transaction (i.e. spending an unspent transaction output or UTXO) the may be multiple signatures in a transaction potentially created by different private keys, or even different people.

As a consequence of these factors, signatures have more parameters than may be immediately obvious, and the details of how signatures are generated can be, and have been, changed in a number of ways.  These parameters are encoded in the [Hash Type](#hash-type).

In addition, as a part of [BCH-UAHF](/protocol/forks/bch-uahf) (activated in block 478,559), the transaction signed format changed from the legacy [Bitcoin (BTC) method](#btc-signatures) to the [Bitcoin Cash (BCH) Signatures](#bch-signatures).  In both cases, there is a signature preimage format (input) and a signature format (output).

### Hash Type

Parameters that change the way a signature hash is generated are encoded in the hash type field.
This field (which is always included in the preimage), is contained in 4 bytes.
The two least significant bits have the following collective meaning:

| Value | Meaning |
|--|--|
| `0x01` | `SIGHASH_ALL`.  This is the default and indicates that all outputs are included in the signature preimage. |
| `0x02` | `SIGHASH_NONE`.  Indicates that no outputs are included in the signature preimage. |
| `0x03` | `SIGHASH_SINGLE`.  Indicates that only the output with the same index as the input the signature is being generated for will be included in the signature preimage. |

In conjunction with the above values, the higher-order bits act as a bitmask with the following meaning:

| Bit | Meaning |
|--|--|
| `0x00000040` | `SIGHASH_FORKID`.  If set, indicates that this signature is for a Bitcoin Cash transaction.  Required following BCH-UAHF, to prevent transactions from being valid on both the BTC and BCH chains. |
| `0x00000080` | `SIGHASH_ANYONECANPAY`.  Indicates that only information about the input the signature is for will be included, allowing other inputs to be added without impacting the signature for the current input. |

Combining these, there are 6 valid signature hash types in Bitcoin Cash.  Only the least significant byte (LSB) is shown in binary, since the rest of the bits are zero.

| Signature hash type                                      | Value (hex) | LSB (bin)   |  Description                                                          |
| -------------------------------------------------------- | ----------- | ----------- | --------------------------------------------------------------------- |
| SIGHASH_ALL \| SIGHASH_FORKID                            | 0x00000041  | 0b01000001  | Signature applies to all inputs and outputs.                          |
| SIGHASH_NONE \| SIGHASH_FORKID                           | 0x00000042  | 0b01000010  | Signature applies to all inputs and none of the outputs.              |
| SIGHASH_SINGLE \| SIGHASH_FORKID                         | 0x00000043  | 0b01000011  | Signature applies to all inputs and the output with the same index.   |
| SIGHASH_ALL \| SIGHASH_ANYONECANPAY \| SIGHASH_FORKID    | 0x000000C1  | 0b11000001  | Signature applies to its own input and all outputs.                   |
| SIGHASH_NONE \| SIGHASH_ANYONECANPAY \| SIGHASH_FORKID   | 0x000000C2  | 0b11000010  | Signature applies to its own input and none of the outputs.           |
| SIGHASH_SINGLE \| SIGHASH_ANYONECANPAY \| SIGHASH_FORKID | 0x000000C3  | 0b11000011  | Signature applies to its own input and the output with the same index.|

## BCH Signatures

In Bitcoin Cash, transaction signature uses the transaction digest algorithm described in [BIP143](https://github.com/bitcoin/bips/blob/master/bip-0143.mediawiki), in order to minimize redundant data hashing in verification and to cover the input value by the signature.

### Preimage Format

| Field | Length | Format | Description |
|--|--|--|--|
| transaction version | 4 bytes | unsigned integer<sup>[(LE)](/protocol/misc/endian/little)</sup> | The value of transaction's version field. |
| previous outputs hash | 32 bytes | hash<sup>[(BE)](/protocol/misc/endian/big)</sup> | A double SHA-256 hash of the set of previous outputs spent by the inputs of the transaction.  See [Previous Outputs](#previous-outputs-hash) for the hash preimage format.<br/><br/>If hash type is "ANYONE CAN PAY" then this is all `0x00` bytes.  |
| sequence numbers hash | 32 bytes | hash<sup>[(BE)](/protocol/misc/endian/big)</sup> | A double SHA-256 hash of the set of sequence numbers of the inputs of the transaction.  See [Sequence Numbers](#sequence-numbers-hash) for the hash preimage format.<br/><br/>If hash type is "ANYONE CAN PAY" then this is all `0x00` bytes.  |
| previous output hash | 32 bytes | hash<sup>[(LE)](/protocol/misc/endian/little)</sup> | The transaction ID of the previous output being spent. |
| previous output index | 4 bytes | unsigned integer<sup>[(LE)](/protocol/misc/endian/little)</sup> | The index of the output to be spent. |
| modified locking script length | variable | [variable length integer](/protocol/format/variable-length-integer) | The number of bytes for `modified_locking_script`. |
| modified locking script | `modified_locking_script_length` bytes | bytes<sup>[(BE)](/protocol/misc/endian/big)</sup> | The subset of the locking script used for signing.  See [Modified Locking Script](#modified-locking-script) |
| previous output value | 8 bytes | unsigned integer<sup>[(LE)](/protocol/misc/endian/little)</sup> | The value of the transaction output being spent. |
| input sequence number | 8 bytes | unsigned integer<sup>[(LE)](/protocol/misc/endian/little)</sup> | The sequence number of the input this signature is for. |
| transaction outputs hash | 32 bytes | hash<sup>[(BE)](/protocol/misc/endian/big)</sup> | A double SHA-256 hash of the outputs of the transaction.  See [Transaction Outputs](#transaction-outputs-hash) for the hash preimage format.  |
| transaction lock time | 4 bytes | unsigned integer<sup>[(LE)](/protocol/misc/endian/little)</sup> | The lock time of the transaction. |
| hash type | 4 bytes | [Hash Type](#hash-type)<sup>[(LE)](/protocol/misc/endian/little)</sup> | Flags indicating the rules for how this signature was generated. |

#### Previous Outputs Hash

The double-SHA256-hash of the following data is used.

For each transaction input in the transaction, append the following information:

| Field | Length | Format | Description |
|--|--|--|--|
| previous transaction hash | 32 bytes | bytes<sup>[(LE)](/protocol/misc/endian/little)</sup> | The hash of the transaction that generated the output to be spent. |
| output index | 4 bytes | unsigned integer<sup>[(LE)](/protocol/misc/endian/little)</sup> | The index of the output to be spent from the specified transaction. |

#### Sequence Numbers Hash

The double-SHA256-hash of the following data is used.

For each transaction input in the transaction, append the following information:

| Field | Length | Format | Description |
|--|--|--|--|
| sequence number | 4 bytes | unsigned integer<sup>[(LE)](/protocol/misc/endian/little)</sup> | The sequence number field of the transaction input. |

#### Modified Locking Script

The locking script included in the signature preimage is, first, dependent on the type of locking script included in the previous output.  For non-[P2SH](/protocol/blockchain/transaction/locking-script#pay-to-script-hash-p2sh) outputs, the locking script itself is used.  However, for P2SH outputs, the redeem script is used instead.

Second, the selected script (locking script or redeem script) is modified as follows.

* Find the [`OP_CODESEPARATOR`](/protocol/blockchain/script#cryptography) operation in the script preceding the expected [signature-verification operation](/protocol/blockchain/script#cryptography) (e.g. `OP_CHECKSIG`).
* Remove all operations before this point.
* Remove any remaining `OP_CODESEPARATOR` operations.

The resulting script is what is included in the signature preimage.

#### Transaction Outputs Hash

If the hash type is `SIGHASH_NONE` then the hash should be all `0x00` bytes.

If hash type is `SIGHASH_SINGLE` then only the output with the same index as the input being signed is included.
If no such output exists (i.e. there are fewer outputs than the index of the input to be signed), this is again all `0x00` bytes.

Otherwise, all outputs of the transaction should be signed (i.e. `SIGHASH_ALL`).

For each transaction output to be signed (per the hash mode), append the following information:

| Field | Length | Format | Description |
|--|--|--|--|
| value | 8 bytes | unsigned integer<sup>[(LE)](/protocol/misc/endian/little)</sup> | The number of satoshis to be transferred. |
| locking script length | variable | [variable length integer](/protocol/formats/variable-length-integer) | The size of the locking script in bytes. |
| locking script | variable | bytes<sup>[(BE)](/protocol/misc/endian/big)</sup> | The contents of the locking script. |

### Signature Format



## BTC Signatures


### Preimage Format


### Signature Format

