<div class="cwikmeta">{
"title":"GETDATA",
"related":["/protocol","/protocol/p2p/inv"]
}</div>

Requests information (generally previously announced via an INV) from a peer.

A GETDATA request is formatted as a vector of INV data:

| compact int |  4 bytes | 32 bytes | ... | additional objects |
|---------------|-----------|------------|--|------|
| [vector](/protocol/p2p/vector) size N elements of | element 1 type | element 1 hash | ... | element N type and hash

### Type

The type of the desired object. See [INV](/protocol/p2p/inv) for specific values

### Hash
The [hash identifier](glossary/hash__identifier) of the desired object.