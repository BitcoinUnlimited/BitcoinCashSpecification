# Block

A block is a collection of transactions that have been committed to the blockchain as a bundle.  The transactions do not necessarily have to be related to one another and, generally, are only associated by virtue of when they were submitted to the Bitcoin Cash network.

Every block contains one coinbase transaction, followed by zero or more additional transactions.  The coinbase transaction is created by the miner of the block and provides an opportunity for them to receive the block reward for successfully mining a block, along with any fees collected from the transactions in the block.  For more information, see [Mining](/protocol/mining).

## Block Headers

Block are identified by the SHA-256 hash of their header.  Since the block header contains the root of a [merkle tree](/protocol/blockchain/merkle-tree) of the transactions in the block, this means the hash is ultimately dependent on both the meta-information about the block as well as the full contents of all of its component transactions.  For more information see [Block Header](/protocol/blockchain/block/block-header).

## Coinbase Transaction

The coinbase, or generation, transaction provides a mechanism for miners to:

 - Receive payment for mining
 - Include a message, known as the "coinbase message," or simply "coinbase," within the block 

To accomplish this, the coinbase transaction is required to have a single input.  However, unlike other transactions, this input is not expected to provide the satoshis that appear in the outputs.  Instead, the satoshis that appear in the outputs of the coinbase transaction are collected from two places: the block reward and transactions fees.

The block reward is provided automatically to the block and represents a continually decreasing incentive for miners.  The block reward started at 5,000,000,000 satoshis (50 BCH) and decreases every 210,000 blocks.

Transaction fees are required by the network for transactions to be relayed across the network.  Satoshis provided as inputs to a transaction, but not consumed by its outputs, are collected by the coinbase transaction as implicit inputs.
