# Standard Messages

The Bitcoin Cash Peer-to-Peer (P2P) Network protocol is a binary protocol used by Full Nodes and [SPV](/protocol/simple-payment-verification) Nodes, usually transmitted via TCP.  The P2P network is similar to a gossip network, where nodes listen for messages and then relays that message to its other peers if it believes that message's content is valid.

## Message Format

The P2P network has a variety of message types.  All P2P messages follow a binary format with the following structure:


| Field | Length | Format |
|--|--|--|--|
| net magic | 4 bytes | byte array<sup>[(BE)](/protocol/misc/endian/little)</sup> |
| command string | 12 bytes | string<sup>[(BE)](/protocol/misc/endian/little)</sup> |
| payload byte count | 4 bytes | integer<sup>[(LE)](/protocol/misc/endian/little)</sup> |
| payload | variable |  |

### Net Magic

The `net magic` field is always `E3E1F3E8`.  Any message received that does not begin with the `net magic` should be ignored.

The `net magic` is designed to be unlikely to occur in normal data--the characters are rarely used upper ASCII, are not valid as UTF-8, and produce a large 32-bit integer with any alignment.  `E3E1F3E8` is the ASCII string, "cash", with each byte's highest bit set.

### Command String

The `command string` is a fixed-length 12 byte ASCII string.  The command string is used to determine the type of message being transmitted.  Messages with unknown an unrecognized `command string` are ignored.

The following messages are considered standard by all node implementations.

| Command String | Name |
| -- | -- |
| version | [Handshake: Version](/protocol/network/messages/version) |
| verack | [Handshake: Acknowledge Version](/protocol/network/messages/verack) |
| ping | [Ping](/protocol/network/messages/ping) |
| pong | [Pong](/protocol/network/messages/pong) |
| addr |  |
| getblocks |  |
| inv |  |
| mempool |  |
| getheaders |  |
| headers |  |
| getdata |  |
| block |  |
| tx |  |
| merkleblock |  |
| notfound |  |
| reject |  |
| sendheaders |  |
| feefilter |  |
| getaddr |  |
| filterload |  |
| filteradd |  |
| filterclear |  |

The following messages are well known, but not implemented by all node implementations.

| Command String | Name | Supported Implementations |
| -- | -- | -- |
| sendcmpct |  |  |
| get_xthin |  |  |
| xthinblock |  |  |
| thinblock |  |  |
| get_xblocktx |  |  |
| xblocktx |  |  |