<div class="cwikmeta">{
"title":"INV",
"related":["/protocol","/protocol/p2p/getdata","/protocol/p2p/filterload","/protocol/p2p/filterclear"]
}</div>

*Notifies peers about the existence of some information (block or transaction)*
Based on selected services in the [VERSION]("/protocol/p2p/version") message, INV messages may not be sent.

If a bloom filter has been sent to this node via [FILTERLOAD](/protocol/p2p/filterload), transaction INVs will only be sent if they match the bloom filter.

| compact int | 4 bytes | 32 bytes |... | 4 bytes | 32 bytes |
|----------|---------|----------|---|---------|----------| 
|[vector](/protocol/p2p/vector) size N of|   type 1  |   hash 1  | | type N | hash N

NOTE: Since a block header is a relatively small data structure, and block propagation speed is an important network metric, a peer may send HEADER messages in place of INV messages when a block arrives.

##### Type
The type of the object that is available.

| Type | Value|
|------|------|
|   1  |  Transaction |
|   2  |  Block |
|   3  |  Filtered Block (partial block with merkle proof)
|   4  |  Compact block
|   5  |  Xthin block (Bitcoin Unlimited)
|   6  |  Graphene Block (Bitcoin Unlimited)

Implementations: [C++](https://github.com/BitcoinUnlimited/BitcoinUnlimited/blob/eb264e627e231f7219e60eef41b4e37cc52d6d9d/src/protocol.h#L477)

##### Hash
The [hash identifier](/glossary/hash__identifier) of the available object.