# Unlocking Script

An unlocking script is a [Script](/protocol/blockchain/script) that is used to prove that a transaction is permitted to spend a given UTXO.  This is accomplished by first executing the unlocking script and then executing the locking script for the UTXO referenced by the transaction input.  If this execution triggers no failures and leaves a single non-zero (TRUE) value on the stack, the UTXO has been successfully unlocked.  One way to look at this is that the unlocking script provides an initial state that acts as an inverse to the previously published locking script.   For more information about how script execution works, see [Script](/protocol/blockchain/script).

## Standard Scripts

While unlocking scripts are generally not restricted in their format, they are often de facto restricted due to the fact that they must create a stack state that allows the locking scripts to complete execution successfully.  For the standard locking scripts that correspond to the below examples, see [Locking Script](/protocol/blockchain/transaction/locking-script).

### Pay to Public Key



### Pay to Public Key Hash



### Pay to Script Hash
