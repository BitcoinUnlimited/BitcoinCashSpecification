<div class="cwikmeta">{
"title":"MERKLEBLOCK",
"related":["/protocol", "/protocol/p2p/filteradd"]
}</div>

*Provides a block header and partial merkle proof tree to show that selected transaction ids exist in the block.*  

Transactions are selected by honest servers if they match the bloom filter installed by the client.  However, note that servers can omit transactions and this cannot be detected except by receiving a MERKLEBLOCK message from an honest server.

All selected transactions are subsequently sent as separate TX messages.  Due to multi-threading on the server, clients should not assume that these TX messages directly follow the MERKLEBLOCK message.

**MERKLEBLOCK Message Format**

| ... | 4 bytes |  ... | ...|
|----|---------------|-----------|------------|
|  [block header](/protocol/p2p/block__header)| # tx in block | [vector](/protocol/p2p/vector) of 32 byte hashes | [vector](/protocol/p2p/vector) of bytes that define the merkle block traversal