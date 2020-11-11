# Transaction Signing

Generally, every input of a [transaction](/protocol/blockchain/transaction) require one or more signature. The signatures enforce (sign) what the transaction looks like and make it impossible for a third party to tamper with without invalidating the transaction. 

This applies to any input whose previous output [locking script](/protocol/blockchain/transaction/locking-script) includes one of the following [operation codes](/protocol/blockchain/script#operation-codes-opcodes): `OP_CHECKSIG`, `OP_CHECKSIGVERIFY`, `OP_CHECKMULTISIG`, `OP_CHECKMULTISIGVERIFY`. In scripts using these opcodes, *signatures* are checked against *public keys* and the transaction signature (as described below).

The `OP_CHECKSIG` and `OP_CHECKSIGVERIFY` opcodes require a sigle signature which is checked against a single public key:

```
<sig> <pubkey> CHECKSIG(VERIFY)
```

For `OP_CHECKMULTISIG` and `OP_CHECKMULTISIGVERIFY` behavior, see [Multisignature spec](/protocol/blockchain/cryptography/multisignature).

## Transaction Digest Algorithm (Preimage Format) 

In Bitcoin Cash, transaction signature uses the transaction digest algorithm described in [BIP143](https://github.com/bitcoin/bips/blob/master/bip-0143.mediawiki), in order to minimize redundant data hashing in verification and to cover the input value by the signature.

Since it is impossible to sign signatures themselves, it is necessary to have a *preimage* which represents the transaction without signatures. Therefore, a preimage must be built for any input which requires a transaction signature.

The preimage consists of the following elements:

| Field                               | Length   | Format                                                               | Description                                                 |
| ----------------------------------- | -------- | -------------------------------------------------------------------- | ----------------------------------------------------------- |
| version                             | 4 bytes  | unsigned integer<sup>[(LE)](/protocol/misc/endian/little)</sup>      | The version of the transaction format. Currently `1` or `2`. |
| previous outputs hash               | 32 bytes | hash<sup>[(BE)](/protocol/misc/endian/big)</sup>                     | The double SHA256 of the serialization of **all** input outpoints (txids + indexes) of the transaction. If the `SIGHASH_ANYONECANPAY` flag is set, it is `0x00...00`. |
| sequences hash                      | 32 bytes | hash<sup>[(BE)](/protocol/misc/endian/big)</sup>                     | The double SHA256 of the serialization of **all** input sequences of the transaction. If `SIGHASH_ANYONECANPAY`, `SIGHASH_SINGLE` or `SIGHASH_NONE` is used, this field is `0x00...00`. |
| previous output transaction id      | 32 bytes | hash<sup>[(LE)](/protocol/misc/endian/big)</sup>                     | The identifier of the transaction containing the previous output, i.e., the output spent by this input. |
| previous output index               | 4 bytes  | unsigned integer<sup>[(LE)](/protocol/misc/endian/little)</sup>      | The index of the previous output inside the transaction.    |
| previous output locking script size | variable | [variable length integer](/protocol/formats/variable-length-integer) | The size of the previous locking script in bytes.           |
| previous output locking script      | variable | bytes<sup>[(BE)](/protocol/misc/endian/big)</sup>                    | The locking script of the output spent by this input. If the previous output is a P2SH output, then this field must be the redeem script. |
| previous output value               | 8 bytes  | unsigned integer<sup>[(LE)](/protocol/misc/endian/little)</sup>      | The value in satoshis of the output spent by this input.    |
| sequence number                     | 4 bytes  | unsigned integer<sup>[(LE)](/protocol/misc/endian/little)</sup>      | The sequence number of this input.                          |
| outputs hash                        | 32 bytes | hash<sup>[(BE)](/protocol/misc/endian/big)</sup>                     | The double SHA256 of the serialization of **all** output values and locking scripts (including size) of the transaction. If `SIGHASH_SINGLE` is used: if this input index is smaller than the number of outputs, this field is the double SHA256 of the output value and locking script of the same index as the input; otherwise it is `0x00...00`. If `SIGHASH_NONE` is used, this field is `0x00...00`. |
| locktime                            | 4 bytes  | unsigned integer<sup>[(LE)](/protocol/misc/endian/little)</sup>      | The locktime of the transaction.                            |
| signature hash type                 | 4 bytes  | unsigned integer<sup>[(LE)](/protocol/misc/endian/little)</sup>      | The signature hash type used to sign this input. See description below.  |

The signing algorithm (whether it is ECDSA or Schnorr algorithm) is applied to **the double SHA256 hash of this preimage**.

## Signature Hash Type

A signature (ECDSA or Schnorr) is ALWAYS followed by the signature hash type used to sign the input. Signature hash type indicates which part of the transaction is hashed to be signed.

Version and locktime are always signed. All inputs are included unless the `SIGHASH_ANYONECANPAY` flag is set.

The signature hash flags are:

| Flag                 | Value (hex) | Value (bin) |  Description                             |
| -------------------- | ----------- | ----------- | ---------------------------------------- |
| SIGHASH_ALL          | 0x01        | 0b00000001  | Sign all outputs.                        |
| SIGHASH_NONE         | 0x02        | 0b00000010  | Sign none of the outputs.                |
| SIGHASH_SINGLE       | 0x03        | 0b00000011  | Sign only the ouput with the same index. |
| SIGHASH_ANYONECANPAY | 0x80        | 0b10000000  | Sign only its own input.                 |
| SIGHASH_FORKID       | 0x40        | 0b01000000  | Bitcoin Cash modifier flag.              |

Signature hash flags are combined using the bitwise OR operator (`|`) in order to get the **signature hash type** of the input. There are 6 valid signature hash types in Bitcoin Cash:

| Signature hash type                                      | Value (hex) | Value (bin) |  Description                                                          |
| -------------------------------------------------------- | ----------- | ----------- | --------------------------------------------------------------------- |
| SIGHASH_ALL \| SIGHASH_FORKID                            | 0x41        | 0b01000001  | Signature applies to all inputs and outputs.                          |
| SIGHASH_NONE \| SIGHASH_FORKID                           | 0x42        | 0b01000010  | Signature applies to all inputs and none of the outputs.              |
| SIGHASH_SINGLE \| SIGHASH_FORKID                         | 0x43        | 0b01000011  | Signature applies to all inputs and the output with the same index.   |
| SIGHASH_ALL \| SIGHASH_ANYONECANPAY \| SIGHASH_FORKID    | 0xc1        | 0b11000001  | Signature applies to its own input and all outputs.                   |
| SIGHASH_NONE \| SIGHASH_ANYONECANPAY \| SIGHASH_FORKID   | 0xc2        | 0b11000010  | Signature applies to its own input and none of the outputs.           |
| SIGHASH_SINGLE \| SIGHASH_ANYONECANPAY \| SIGHASH_FORKID | 0xc3        | 0b11000011  | Signature applies to its own input and the output with the same index.|
