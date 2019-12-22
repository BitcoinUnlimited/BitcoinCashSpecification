# Script

Bitcoin Cash transactions make use of a scripting language to authorize and secure transfers.
While, colloquially, there is a tendency to refer to transactions as "sending" Bitcoin Cash to "an address", that is merely an abstraction.
In fact, the only thing that permits the spending of existing [UTXOs](/protocol/blockchain/transaction#transaction-outputs) is the successful execution of a script.
The only thing preventing the spending of newly created UTXOs is the difficulty of producing a successfully executing script.
Through the use of cryptographic signatures and hash functions, such scripts are often designed specifically to be difficult to produce unless you are the intended spender of a given UTXO, though that need not necessarily be the case.

For more information on how Transactions are commonly secured, see [Locking Script](/protocol/blockchain/transaction/locking-script).

This page will focus on how the scripts are run, what they are capable of, and what limitations they have.

## Script Execution

Scripts are executed using a stack-based memory model.