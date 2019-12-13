<div class="cwikmeta">{
"title":"Bloom Filter",
"related":["/protocol/p2p/filterload"]
}</div>

A bloom filter is an imperfect but efficient set membership test.  The filter will never incorrectly report that member is NOT in the set.  However, it may report that a member is in a set when it is actually not.  See [wikipedia](https://en.wikipedia.org/wiki/Bloom_filter), or the [original paper](https://dl.acm.org/citation.cfm?id=362686.362692) for details.   

Therefore bloom filters are often used during set membership testing as a quick pre-check to eliminate most elements.

### Use in the Bitcoin Peer-to-Peer Protocol
Bloom filters are used in the bitcoin peer-to-peer protocol to filter the transactions and blocks that a client is sent from another node.  The client first "installs" a bloom filter that contains information about the objects it is interested in by sending a [FILTERLOAD](/protocol/p2p/filterload) message to a peer.   The peer will subsequently only provide objects that match the installed filter.

At a minimum, an [SPV](/glossary/SPV) client must insert addresses and [outpoints](/glossary/outpoint) into the bloom filter.  The addresses will cause every transaction that pays INTO the wallet to be reported, and the outpoints will cause every transaction that pays OUT OF the wallet to be reported.

Bitcoin Bloom filters have an additional feature.  Based on the setting of a flag byte, it is possible to direct the peer to automatically insert new data into the bloom filter based on transactions that match.  In particular, outpoints will be automatically inserted that correspond to the outputs of addresses that match the filter.  This facility is extremely important because if a receive and spend occur within the same transaction, the client does not have the opportunity to update the filter.  Bloom filters should therefore be sized with the expectation that new elements will be inserted.

However, due to the bloom filter's tendency to have false positives, these insertions means that unnecessary data will be inserted into the filter, creating even more false positives in a positive feedback loop.  Eventually the node will start sending a lot of unnecessary traffic to the client.  Therefore, it is essential for clients to periodically refresh (using [FILTERLOAD](/protocol/p2p/filterload)) the bloom filter to remove these unnecessary entries.
