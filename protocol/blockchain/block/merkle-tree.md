# Merkle Tree

A [merkle tree](https://en.wikipedia.org/wiki/Merkle_tree) is a data structure designed to build a hash of many separate pieces of data and allowing efficient recalculation of the hash when items are added, removed, or changed.

In Bitcoin Cash, the transactions in a [block](/protocol/blockchain/block) are built into a merkle tree.  The root of this tree, or **merkle root**, is a hash representing a compound, indirect hash of all of the items added to the merkle tree (i.e. transactions) and is included in the [block header](/protocol/blockchain/block/block-header), ensuring the block hash is dependent (indirectly) on all of the transactions that are included in the block.

Using a merkle tree to manage hashing the transactions in a block enables efficient addition of newly submitted transactions to a block being mined.  Rather than re-hashing the entirety of the data of the transactions to be included in the block, the new transaction can be hash and then a small number of hashes can be recalculated, leading up to the merkle root.  For large blocks this can heavily reduce the amount of data to be (re-)hashed.