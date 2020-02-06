<div class="cwikmeta" style="visibility:hidden;">
{
"title":"PING",
"related":["/protocol/p2p/pong"]
} </div>

Connection keep-alive, "aliveness" and latency discovery.

|  nonce  |
|---------|
| 8 bytes |

If a node receives a PING message, it replies as quickly as possible with a [PONG](/protocol/p2p/pong) message with the provided *nonce*.