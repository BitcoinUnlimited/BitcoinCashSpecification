<div class="cwikmeta">{
"title":"HEADERS",
"related":["/protocol","/protocol/p2p/getheaders"]
}</div>

*Provides a contiguous set of block headers*  

**HEADERS Message Format**

| compact int  | ... | compact int |
|----|---------------|-----------|------------|
| [vector](/protocol/p2p/vector) size N containing: |  [block header](/protocol/p2p/block__header)| number tx in block | 

*[vector](/protocol/p2p/vector) size N containing*:  This message contains a vector of block headers.  No more than 2000 block headers may be sent at one time.

*block header*:  A block header.  Block headers in this array MUST be sequential.

*number tx in block*: **DEPRECATED** This field should be ignored and servers may send 0 regardless of the actual number of transactions in the block.