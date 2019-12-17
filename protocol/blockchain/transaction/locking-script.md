# Locking Script

A locking script is a [Script](/protocol/blockchain/script) that is used dictate how funds can be spent in the future.  Locking scripts do this by defining a set of operations that must end in a successful script execution.  The rest of the script execution is to be provided in an [unlocking script](/protocol/blockchain/transaction/unlocking-script) in a future transaction's input.  It is important that the locking script is executed after the unlocking script.  If the unlocking script were run second, it could end by removing any values currently on the stack and replacing it with a success value.  For more information about how script execution works, see [Script](/protocol/blockchain/script).

## Standard Scripts
