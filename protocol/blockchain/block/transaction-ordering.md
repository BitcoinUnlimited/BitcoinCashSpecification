# Transaction Ordering

Transactions in a block are inherently ordered by virtue of their inclusion in the [merkle tree](/protocol/blockchain/block/merkle-tree).  Furthermore, the [coinbase transaction](/protocol/blockchain/block#coinbase-transaction) is required to be first in the block.  After that, the order of any remaining transactions in the block is dependent on when that block was mined.  For new blocks, the [canonical transaction ordering](#canonical-transaction-ordering) rule is in effect.  For old blocks, see [topological transaction ordering](#legacy-transaction-ordering).

## Canonical Transaction Ordering

With canonical transaction ordering, or CTOR, the transactions after the coinbase transaction are required to be sorted in lexicographical order by transaction hash.  New blocks will not be accepted by the network unless they follow this constraint.  CTOR went into effect at 1542300000 unix time (November 15, 2018 4:40:00 PM GMT).  See [HF20181115](/protocol/forks/HF20181115) for more information on this change.

## Topological Transaction Ordering

With topological transaction ordering, transactions after the coinbase transaction were required to abide by the following constraint: if a transaction B spends outputs created by transaction A, transaction B must appear after transaction A in the block.  Otherwise, transactions could appear in any order.