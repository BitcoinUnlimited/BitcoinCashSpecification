# Transaction

A transaction is how transfers are made in the blockchain. It comprises of a set of [transaction inputs](#transaction-inputs) which will be spent to a set of [transaction outputs](#transaction-outputs). The blockchain mining and full node software ensures that every transaction follows the blockchain's rules before admitting the transaction into a block. Verification of a transaction ensures that the inputs have not already been spent, that the total number of satoshis, the smallest quantity of Bitcoin Cash, provided as input is greater than or equal to the number of satoshis output (any extra is given to the miner as a transaction fee), and that the transaction is syntactically and cryptographically valid.

## Format



## Transaction Inputs

Transaction inputs are the "debits" of Bitcoin Cash and not only designate the satoshis that will be transferred as a part of the transaction, but they also provide proof of ownership via the [unlocking script](/protocol/blockchain/transaction/unlocking-script).  A transaction input references a unspent transaction output, or UTXO, from a prior transaction.  If a transaction would like to spend multiple UTXOs, it must have multiple inputs, one for each UTXO.

## Transaction Outputs

Transaction outputs are the "credits" of Bitcoin Cash.  Each transaction output denotes a number of satoshis and the requirements are spending them.  These requirements take the form of a [locking script](/protocol/blockchain/transaction/locking-script) and can equate to anything from the satoshis only being spendable by the owner of a specific private key, to anyone being able to spend them, to no one being able to spend them.  While a transaction output has not been spent by another transaction (i.e. had its locking script "unlocked" by an input of a valid transaction) it is referred to as an unspent transaction output, or UTXO.  After the output has been spent, it is referred to as a previous output, or PrevOut.