<div class="cwikmeta">{
"title":"REJECT",
"related":["/protocol"]
}</div>

*Notifies peer that a message is invalid*


| [compact int](/protocol/p2p/compact__int.md) N (max 12) | N bytes | 1 byte | [compact int](/protocol/p2p/compact__int.md) M (max 111) | M bytes | 
|----------------|---------|-------------|---------------|---------| 
| command length | command | error code  | reason length | reason string |

*command length* and *command* form a [vector](/protocol/p2p/vector.md) of bytes that identify the type of the problem message.  These bytes (less padded nulls) match the *command* field in the protocol [message envelope](/protocol.md).

*reason length* and *reason string* form a [vector](/protocol/p2p/vector.md) of bytes that is a string providing a human language explanation of the reason the message was rejected.  This string is subject to change so client software **SHOULD NOT** use it programatically.

*error code* provides a succinct machine-interpretable reason why the message was rejected.

### Reject Error Codes

|    Name    | Value | Response To |  Description
|-------------|-------|---------------|-----------|
| REJECT_MALFORMED | 0x01 | any | message cannot be deserialized |
| REJECT_INVALID | 0x10 | BLOCK, TX, FILTERSIZEXTHIN | block or transaction is invalid, or xthin filter size is too small | 
| REJECT_OBSOLETE | 0x11 | VERSION | This node no longer supports your client protocol version |
| REJECT_DUPLICATE | 0x12 | unused | |
| REJECT_NONSTANDARD | 0x40 | TX | Transaction is [non-standard](/standard__transactions.md), or [not final](/final__transactions.md) as per BIP68 |
| REJECT_DUST  | 0x41 | TX | Transaction has an output that is too small |
| REJECT_INSUFFICIENTFEE | 0x42 | TX | Transaction does not pay enough to be relayed |
| REJECT_CHECKPOINT | 0x43 | unused | |
| REJECT_WAITING | 0x44 | TX | This transaction is [not final](/final__transactions.md) |