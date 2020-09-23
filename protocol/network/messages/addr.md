<div class="cwikmeta">{
"title":"ADDR",
"related":["/protocol","/protocol/network/messages/getaddr"]
}</div>

# Response: Addresses ("addr")

Provide information about other prospective P2P protocol peers.  Peers SHOULD not send this message unsolicited (see [getaddr](/protocol/network/messages/getaddr)), and nodes that receive an unsolicited ADDR message MUST ignore it.  This behavior helps prevent eclipse and partitioning attacks by not allowing an attacker to aggressively seed peer connection tables with its own nodes.

## Message Format

| Field | Length | Format | Description |
|--|--|--|--|
| vector length N | variable | [compact int](/protocol/p2p/compact__int.md) | The following fields are repeated N times |
| time 0 | 4 bytes | unsigned int<sup>[(LE)](/protocol/misc/endian/little)</sup> | last known alive time |
| services 0 | 8 bytes | unsigned int<sup>[(LE)](/protocol/misc/endian/little)</sup> bit field | the services this node supports |
| IP address 0 | 16 bytes | bytes | connect using this IP address
| port 0 | 2 bytes | unsigned integer<sup>[(BE)](/protocol/misc/endian/little)</sup> | connect using this IP port 
| ... |
| time N, etc


*services* is a bit field describing supported functionality, that is also used in the [version](/protocol/p2p/version) message.  See [services field](/protocol/p2p/services__field) for bit definitions.

*time* is a 4 byte unsigned little-endian integer denoting the last time in epoch seconds that this node was known to be "live".

*IP address* is the IPv4 or IPv6 address of the prospective node.

*port* is the IP port of the prospective node, serialized as an unsigned 2 byte big-endian value.