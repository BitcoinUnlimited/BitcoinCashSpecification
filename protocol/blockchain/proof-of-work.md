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