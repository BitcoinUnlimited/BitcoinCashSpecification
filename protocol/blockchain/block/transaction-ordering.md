# Transaction Ordering

Transactions in a Block are inherently ordered by virtue of their inclusion in the [Merkle Tree](/protocol/blockchain/block/merkle-tree).
Furthermore, the [Coinbase Transaction](/protocol/blockchain/block#coinbase-transaction) is required to be the first Transaction in the Block.
After that, the order of any remaining Transactions in the block is dependent on when that Block was mined.  For new blocks, the [Canonical Transaction Ordering](#canonical-transaction-ordering) rule is in effect.
For historic blocks, see [Topological Transaction Ordering](#legacy-transaction-ordering).

## Canonical Transaction Ordering

With canonical transaction ordering (CTOR), the Transactions after the Coinbase Transaction are required to be sorted in lexicographical order by Transaction Hash.
New Blocks will not be accepted by the network unless they follow this constraint.
CTOR went into effect at 1542300000 unix time (November 15, 2018 4:40:00 PM GMT).

See [HF20181115](/protocol/forks/HF20181115) for more information on this change.

## Topological Transaction Ordering

With topological transaction ordering, Transactions after the Coinbase Transaction were required to abide by the following constraint: if a transaction B spends outputs created by transaction A, transaction B must appear after transaction A in the block.
Otherwise, transactions could appear in any order.