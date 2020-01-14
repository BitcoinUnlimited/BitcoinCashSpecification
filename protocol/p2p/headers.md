<div class="cwikmeta">{
"title":"HEADERS",
"related":["/protocol","/protocol/p2p/getheaders"]
}</div>

*Provides a contiguous set of block headers*  

## HEADERS Message Format

|Size in bytes|Description|Data type|
|-------------|-----------|---------|
|1+           | Number of headers | varint|
|(81 + (1+)) * count  |[Block headers](/protocol/blockchain/block/block-header/) and number of txs in the block|Blockheader + varint|

No more than 2000 block headers may be sent at one time. Block headers in this array MUST be sequential, ordered by height and without range gaps.

The number of txs in header is **DEPRECATED** and should be ignored. Nodes are allowed to send 0 regardless of the actual number of transactions in the block.
