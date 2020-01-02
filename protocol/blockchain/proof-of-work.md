# Proof of Work

Bitcoin Cash uses a [hashcash](https://en.wikipedia.org/wiki/Hashcash)-like algorithm as a primary metric for validating new blocks.
The [block header](/protocol/blockchain/block/block-header) is hashed, its nonce is changed, and it is hashed again, until the resulting hash begins with an expected number of zero-bits.
This repeated hashing with an updated nonce is referred to as mining.
For more information on how the number of zero-bits required is determined, see [Difficulty Adjustment Algorithm](/protocol/blockchain/proof-of-work/difficulty-adjustment-algorithm).
For more information on how mining is performed in practice, see [Mining](/protocol/blockchain/proof-of-work/mining).

Because SHA-256 is used to hash block headers, and it is not known how to find a input matching a certain output format, the proof-of-work system employed by Bitcoin Cash distributes the following quasi-randomly to the individual that successfully creates a block that meets the proof-of-work requirements:

 1. The responsibility of determining which transactions will appear in the block.
 2. The receipt of the block reward for the block.
 3. The receipt of the fees for the block.

This is only quasi-random because the likelihood of building a block with an appropriate hash is directly proportional to the computational power (often referred to as hashing power), available to each individual mining blocks.

## Extra Nonce

Ideally in such a proof-of-work system, the dynamic parameters of the data being hashed (i.e. the block header) would provide enough variability to guarantee any possible output of the hash function used.
However, SHA-256 outputs 32 bytes and the only part of the block header that can be changed rapidly are the nonce, which is only 4 bytes long, and the timestamp, which while also 4 bytes must remain close to the current time.
As a result, there was a need for additional data to be varied.

The only other parameter of the block header that a miner has any power over is the merkle root.
In order to change the merkle root, the transactions in the block would need to be changed.
But since the [coinbase transaction](/protocol/blockchain/block#Coinbase%20Transaction) is already created by the miner of the block, and updating its hash would allow for efficient re-calculation of the merkle root, putting this "extra nonce" in the coinbase transaction was the logical conclusion.
Ultimately, the extra nonce is included as a part of the coinbase message, usually following the block height that is required to be first.