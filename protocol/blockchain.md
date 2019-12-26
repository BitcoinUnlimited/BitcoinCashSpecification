# Blockchain

## What is a Blockchain

The Bitcoin Cash Blockchain is a linear collection of [Blocks](/protocol/blockchain/block).
The "state" of the blockchain is calculated by sequentially executing each set of changes (in the form of [Transactions](/protocol/blockchain/transaction)) included in each Block.

When a Block is created it includes the [Hash](/protocol/blockchain/hash) of the previous block; this cryptographically ensures the Block's parent block cannot be changed without completely rebuilding the new block.
This chaining structure is similar to [Git](//git-scm.com) commits&mdash;where a block is analogous to a commit (a set of file changes), and each commit's parent is tied directly to its parent, forming a chain.
Unlike Git, in order to create a commit (i.e. a Block) a certain amount of work must be performed.
This [Proof of Work](/protocol/blockchain/proof-of-work) prevents blocks from being created instantly, and is the mechanic that prevents old history from being rewritten, since modifying a block would change its hash and disconnect it from its previous children.
Therefore, changing a block requires that each of the child blocks be recreated as well.
With this property, changing old blocks becomes nearly impossible as long as the Work to create a block is sufficiently time-intensive.

## Block Height

Blocks are applied sequentially, and therefore for a given chain, a block can be identified by its distance from the Genesis Block.
This distance is commonly referred to as `Block Height`.
There may be multiple blocks at the same `Block Height` if two blocks are built that share a previous Block's Hash.
Multiple Blocks with the same `Block Height` are often referred to as "sibling" Blocks.
Sibling Blocks can happen if a Block is created with sufficient Work before the mining Node has been made aware of the new (Sibling) Block.
Sibling Blocks are incompatible with one another, and eventually one will become orphaned.
The [Genesis Block](/protocol/blockchain#genesis-block) is the first Block in a chain, and has a `Block Height` of `0` (as its distance to the Genesis Block is zero).

As of [BIP-0034](/blockchain/forks/bip-34), the `Block Height` is included within the [Coinbase Transaction](/protocol/blockchain/block#coinbase-transaction).

## Work

The longest valid chain with the most [Proof of Work](/protocol/blockchain/proof-of-work) is generally considered to be the main chain.
Work can be quickly verified by evaluating the Block's [Difficulty](/protocol/blockchain/proof-of-work/difficulty).
The [Chain Work](/protocol/blockchain/proof-of-work#chain-work) is the summation of all work done up to a point on the Blockchain.

## Blockchain Reorganization


## Genesis Block