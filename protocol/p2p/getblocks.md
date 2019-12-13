<div class="cwikmeta">{
"title":"GETBLOCKS",
"related":["/protocol","/protocol/p2p/getdata","/protocol/p2p/getheaders"]
}</div>

Request the sequence of blocks that occur after a specific block.  If the specified block is on the server's most-work chain, the server responds with a set of up to 500 [INV](/protocol/p2p/inv) messages identifying the next blocks on that chain.  If the specified block is not on the most-work chain, the server uses block information in the *locator* structure to determine the fork point and provides [INV](/protocol/p2p/inv) messages from that point.

|        locator                                                                                | stop at hash | 
|-----------------------------------------------------------------------------------------------|----------|
| [vector](/protocol/p2p/vector) of 32 byte block [hash identifiers](/glossary/hash__identifier)| 32 bytes |


### Locator

See [GETHEADERS](/protocol/p2p/getheaders) for a detailed description of the Locator object.

The response will begin at **the child of** the first hash in the locator list that matches a block hash identifier held by the responder.  If no hashes match, there will be no INV messages sent. 

*Use an empty locator to get an INV for block 1, and GETHEADERS that block to discover the genesis block hash in the prevBlock field*

### Stop At Hash

The sender will stop sending INVs if it encounters this hash.  If the hash is never encountered, the sender will stop after 500 INV messages or when it hits the blockchain tip.

Server Implementations: [Bitcoin Unlimited](https://github.com/BitcoinUnlimited/BitcoinUnlimited/blob/bucash1.7.0.0/src/net_processing.cpp#L1077)
