# Script

Bitcoin Cash transactions make use of a scripting language to authorize and secure transfers.
While, colloquially, there is a tendency to refer to transactions as "sending" Bitcoin Cash to "an address", that is merely an abstraction.
In fact, the only thing that permits the spending of existing [UTXOs](/protocol/blockchain/transaction#transaction-outputs) is the successful execution of a script.
The only thing preventing the spending of newly created UTXOs is the difficulty of producing a successfully executing script.
Through the use of cryptographic signatures and hash functions, such scripts are often designed specifically to be difficult to produce unless you are the intended spender of a given UTXO, though that need not necessarily be the case.

For more information on how Transactions are commonly secured, see [Locking Script](/protocol/blockchain/transaction/locking-script).

This page will focus on how the scripts are run, what they are capable of, and what limitations they have.

## Script Execution

Scripts are executed using a stack-based memory model and have an intentionally restricted set of available operations.  Unlike the common general-purpose programming languages your are probably aware of, Script (the term for the language itself) does not allow for loops, persistent state/memory across script executions, or the definition of functions.  Instead, scripts are expected to contain whatever data they need and use the available operations to prove transaction validity.

Scripts are run when validating transactions, and successful execution of all of the scripts defined by the transaction is a necessary, but not sufficient, condition for transaction validity.  See [Transaction Validation](/protocol/blockchain/transaction-validation) for more details.

As a part of validating a transaction, a script is built for each input spent by the transaction.  Each script is the concatenation of the [unlocking script](/protocol/blockchain/transaction/unlocking-script) provided with the input definition (which is used that the beginning of the script) and the locking script provided by the [previous output](/protocol/blockchain/transaction#transaction-outputs) being referenced (which is the end of the script).  The exception to this is [pay to script hash](/protocol/blockchain/transaction/locking-script#standard-scripts), which has an altered execution workflow.  In general, though, this combined unlocking/locking script is then executed and considered successful if and only if the following conditions are met:

 - **Non-Zero Value** - after execution the top of the stack must contain a non-zero (TRUE) value.
 - **No Stack Overflows** - no operation should attempt to pop a value from the stack when the stack is empty.
 - **Clean Stack** - after execution the stack must only contain a single value, which must be non-zero (TRUE).  Added in [HF20181115](/protocol/forks/HF20181115).

Additionally, in order for the combined script to be valid, the following must be true:

 - **Non-Empty Scripts** - both the locking and unlocking scripts must be non-empty.
 - **Max Script Length** - the locking and unlocking script must each be less than the max script length of 10,000 bytes (for a combined script maximum of 20,000 bytes).
 - **Contained Control Flow** - an IF/ELSE block cannot start in the unlocking script and end in the locking script, the script must be in the top-level scope when the locking script execution begins.
 - **Permitted Operations Only** - the locking script must not contain.
 - **Push Only** - the unlocking script must contain only push operations (i.e. those with op codes 0x60 or less).  Added in [HF20181115](/protocol/forks/HF20181115).