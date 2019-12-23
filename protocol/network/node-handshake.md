# Node Handshake

When nodes connect the issue a connection handshake in order to ensure compatibility between the two nodes.
The handshake informs the peer of its:

- Network Protocol Version
- Block Height
- Supported [Network Services](/protocol/network/messages/version#services-bitmask)

Either Node may then decide to disconnect from the peer.

Neither Node should send any data other than a [Version](/protocol/network/messages/version) message to the peer until it has also received a Version message.
Once a node has received (and sent) a Version message, it may send a [Verack](/protocol/network/messages/verack) message.
Once each Node has sent and received a Verack message, normal node operation may begin.

## Sequence Diagram

When a local Node initiates a connection to a remote Node, the remote Node will remain silent it receives a version message.

```
# SequenceDiagram

title Node Handshake

Local->>Remote: Sends Version Message
note right of Remote: Version Message contains Local's Version Number.
Remote->>Local: Sends Version Message
note left of Local: Version Message contains Remote's Version Number.
Remote->>Local: Sends Verack Message
note left of Local: Local uses the lower of the two Version Numbers, if compatible.
Local->>Remote: Sends Verack Message
note right of Remote: Remote uses the lower of the two Version Numbers, if compatible.
```