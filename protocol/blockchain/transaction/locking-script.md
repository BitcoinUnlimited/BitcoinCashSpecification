# Locking Script

A locking script is a [Script](/protocol/blockchain/script) that is used dictate how funds can be spent in the future.  Locking scripts do this by defining a set of operations that must end in a successful script execution.  The rest of the script execution is to be provided in an [unlocking script](/protocol/blockchain/transaction/unlocking-script) in a future transaction's input.  Every locking script is executed after the unlocking script.  If the unlocking script were run second, it could end by removing any values currently on the stack and replacing it with a success value.  For more information about how script execution works, see [Script](/protocol/blockchain/script).

## Standard Scripts

### Pay to Public Key (P2PK) <img src="/_static_/images/warning.png" />

The P2PK locking script expects the unlocking script to push a signature to the stack.  If the signature is valid for the specified public key in the locking script, the output is allowed to be spent.

| Operation | Description |
|--|--|
| [push data](/protocol/blockchain/script/opcodes/push-data) (public key) | Add the recipient's public key to the stack.  The data pushed must be either a compressed or uncompressed public key with appropriate length for the type for the script to be recognized as P2PK. |
| [OP_CHECKSIG](/protocol/blockchain/script/opcodes/op-checksig) | Check the public key at the top of the stack against the signature below it on the stack. |

<img src="/_static_/images/warning.png" /> **NOTE:** Pay to Public Key is a largely obsolete type of locking script due to its property of leaking the public key of the recipient before the output is unlocked, resulting in:

1. More data to be transferred to request funds, since a public key is larger than the addresses used in other standard scripts.
2. Decreased security in the event of a break in the ECDSA signature algorithm.  That is, if it ever becomes possible to create a signature using a public key (not currently known to be possible), the public key is readily available.
3. Decreased privacy for the recipient, since anyone aware of who owns the public key knows that they can spend the locked funds.

### Pay to Public Key Hash (P2PKH)

Pay to Public Key Hash is a widely used standard locking script format, that works similarly to P2PK but instead of pushing the public key, it pushes a hash of the public key, commonly referred to as an address.  This heavily reduces the risks associated with a plain P2PK script as the hashing algorithms used provide a considerable barrier to determining the public key a priori.  To spend an output locked with this type of script, the unlocking script is expected to push a signature and then the public key corresponding to the private key that created the signature.  If that public key hashes to the expected address, and the signature is valid, the output is allowed to be spent.

| Operation | Description |
|--|--|
| [OP_DUP](/protocol/blockchain/script/opcodes/op-dup) | Copy the value at the top of the stack (public key of the recipient). |
| [OP_HASH160](/protocol/blockchain/script/opcodes/op-hash160) | Perform a SHA-256 then a RIPEMD-160 on the copied value. |
| [push data](/protocol/blockchain/script/opcodes/push-data) (20 bytes) | Push the expected 20 byte address. |
| [OP_EQUALVERIFY](/protocol/blockchain/script/opcodes/op-equalverify) | Verify that the hash of the copied value matches the expected hash that was pushed. |
| [OP_CHECKSIG](/protocol/blockchain/script/opcodes/op-checksig) | Verify that the stack now contains only a public key (which was duplicated, hashed, and checked against the expected value) and a signature and verify that the signature is valid for that public key. |

### Pay to Script Hash (P2SH)

Pay to Script Hash is used to require the spender of an output to include a specific set of operations in their unlocking script.  To achieve this, the unlocking script is expected to end by pushing data to the stack that is the expected script to be executed. Once this data is verified to match the expected script hash, this internal script, commonly referred to as the redeem script, is executed on the pre-locking-script-execution stack.  If this redeem script finishes execution successfully, the output is allowed to be spent.

| Operation | Description |
|--|--|
| [OP_HASH160](/protocol/blockchain/script/opcodes/op-hash160) | Hash the data at the top of the stack, this should be the script to be executed. |
| [push data](/protocol/blockchain/script/opcodes/push-data) (20 bytes) | Push the expected redeem script hash. |
| [OP_EQUAL](/protocol/blockchain/script/opcodes/op-equal) | Verify that the hash of the provided script is equal to the expected hash. |

Due to the nature of this type of locking script, the following steps must be performed by a node executing this script:

1. After executing the unlocking scripts, create a copy of the stack for later use.  Since the value at the top of the stack (the redeem script) will be consumed by the locking script, we want to return to this state after verifying that the hash is correct.
2. Execute the locking script, and ensure that the result of the OP_EQUAL is TRUE.  If not, fail the script execution and treat the transaction as invalid.
3. Return the stack to the pre-locking-script-execution state.  Remove the (now verified) redeem script and interpret it as a new set of operations to be executed.
4. Execute the redeem script against the new state of the stack (with any values places on the stack by the unlocking script *except* the redeem script).
5. Evaluate the success of the script as usual (a single non-zero value left on the stack).

The P2SH form places all interesting constraints in the redeem script, whereas previously these constraints were placed in the locking script. It is arguably better to place constraints in the redeem script for several reasons:

1.  Simple Addresses: P2SH allows the recipient to have funds sent to any constraint script, by providing the sender with an address. Previously, a new address form had to be created for every new script type, or in theory the entire constraint script could be sent from recipient to sender (no protocol to do this has been specced or implemented).
2.  Recipient-Centric Redemption: In P2PKH scripts, the sender requires that the funds are redeemed using a signature.  In the event that the recipient wants to place different requirements on spending the funds, they would have to provide their ideal locking script to the sender and have the sender potentially incur additional fees due to the increased transaction size.  Instead, P2SH allows the recipient to create an address based on their desired spending criteria.  When the recipient attempts to spend the funds, they them provide the script, revealing the spending requirements and paying the transaction fees themselves.
3.  Node Space Efficiency: P2SH defers providing the redeem script until it is used, replacing it with a 20 byte hash. For non-trivial scripts, this will make the UTXO set smaller, since the locking scripts will be smaller.
4.  Privacy: P2SH does not reveal the script contents until it is spent. One drawback of this is that it is not possible to determine whether certain instructions are in use or not. This makes retirement of opcodes impossible. However, it's also possible for people to create transactions and not commit them to the blockchain, so the viability of opcode retirement is questionable anyway.