<div class="cwikmeta">{
"title":"ADDR",
"related":["/protocol","/protocol/p2p/getaddr"]
}</div>

Provide information about other prospective P2P protocol peers.  Peers SHOULD not send this message unsolicited, and nodes that receive an unsolicited ADDR message MUST ignore it.  This behavior helps prevent eclipse and partitioning attacks by not allowing an attacker to aggressively seed peer connection tables with its own nodes.

|              compact int               | 4 bytes | 8 bytes  |  16 bytes | 2 bytes |
|----------------------------------------|---------|----------|----------|---------|
|[vector](/protocol/p2p/vector) size N of| time | services |  IP address | port

*services* is a bit field describing supported functionality, that is also used in the [version](/protocol/p2p/version) message.  See [services field](/protocol/p2p/services__field) for bit definitions.

*time* is a 4 byte unsigned little-endian integer denoting the last time in epoch seconds that this node was known to be "live".

*IP address* is the IPv4 or IPv6 address of the prospective node.

*port* is the IP port of the prospective node, serialized as an unsigned 2 byte big-endian value.