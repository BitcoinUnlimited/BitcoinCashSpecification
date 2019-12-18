# Locking Script

A locking script is a [Script](/protocol/blockchain/script) that is used dictate how funds can be spent in the future.  Locking scripts do this by defining a set of operations that must end in a successful script execution.  The rest of the script execution is to be provided in an [unlocking script](/protocol/blockchain/transaction/unlocking-script) in a future transaction's input.  It is important that the locking script is executed after the unlocking script.  If the unlocking script were run second, it could end by removing any values currently on the stack and replacing it with a success value.  For more information about how script execution works, see [Script](/protocol/blockchain/script).

## Standard Scripts

### Pay to Public Key (P2PK)

Pay to Public Key is a largely obsolete type of locking script, though it is arguably the most straightforward.  It expects the unlocking script to push a signature to the stack.  If the signature is valid and was made by the private key corresponding to the public key in the locking script, the funds are allowed to be transferred.

| Operation | Description |
|--|--|
| [push data](/protocol/blockchain/script/opcodes/push-data) (public key) | Add the recipient's public key to the stack.  The data pushed must be either a compressed or uncompressed public key with appropriate length for the type for the script to be recognized as P2PK. |
| [OP_CHECKSIG](/protocol/blockchain/script/opcodes/op-checksig) | Check the public key at the top of the stack against the signature below it on the stack. |

This type of locking script is no longer recommended due to the fact that it leaks the public key of the recipient.  This results in:

1. More data to be transferred to request funds, since a public key is larger than the addresses used in other standard scripts.
2. Decreased security in the event of a break in the ECDSA signature algorithm.  That is, if it ever becomes possible to create a signature using a public key (not currently known to be possible), the public key is readily available.
3. Decreased privacy for the recipient, since anyone aware of who owns the public key knows that they can spend the locked funds.

### Pay To Public Key Hash (P2PKH)

Pay to Public Key Hash is a widely used standard locking script format, that works similarly to P2PK but instead of pushing the public key, it pushes a hash of the public key, commonly referred to as an address.  This heavily reduces the risks associated with a plain P2PK script as the hashing algorithms used provide a considerable barrier to determining the public key a priori.  To spend an output locked with this type of script, the unlocking script is expected to push a signature and then the public key corresponding to the private key that created the signature.  If that public key hashes to the expected address, and the signature is valid, the funds are allowed to be transferred.

| Operation | Description |
|--|--|
| [OP_DUP](/protocol/blockchain/script/opcodes/op-dup) | Copy the value at the top of the stack (public key of the recipient). |
| [OP_HASH160](/protocol/blockchain/script/opcodes/op-hash160) | Perform a SHA-256 then a RIPEMD-160 on the copied value. |
| [push data](/protocol/blockchain/script/opcodes/push-data) (20 bytes) | Push the excepted 20 byte address. |
| [OP_EQUALVERIFY](/protocol/blockchain/script/opcodes/op-equalverify) | Verify that the hash of the copied value matches the expected hash that was pushed. |
| [OP_CHECKSIG](/protocol/blockchain/script/opcodes/op-checksig) | Verify that the stack now contains only a public key (which was duplicated, hashed, and checked against the expected value) and a signature and verify that the signature is valid for that public key. |

### Pay To Script Hash (P2SH)

