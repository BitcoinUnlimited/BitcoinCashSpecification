<div class="cwikmeta">{
"title":"XUPDATE",
"related":["/protocol","/protocol/p2p/xversion"]
}</div>

*Notifies peers about an XVERSION configuration value update*

This message notifies a peer about changes to protocol parameters.  It follows the same format as [XVERSION](/protocol/p2p/xversion.md) protocol parameters.  Implementations **SHOULD** only send changed parameters, rather than every parameter.   Note that some XVERSION parameters are not changeable and therefore will be ignored if they appear in this message.  

See the [XVERSION](/protocol/p2p/xversion.md) message for detailed information about each parameter.

| compact int | compact int | variable bytes |... | compact int | 32 bytes |
|----------|---------|----------|---|---------|----------| 
|[vector](/protocol/p2p/vector) size N of|   key 1  | [vector](/protocol/p2p/vector) of bytes  | | key N | [vector](/protocol/p2p/vector) of bytes


### Support
Supported by: **Bitcoin Unlimited**