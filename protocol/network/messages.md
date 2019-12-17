# P2P Network Messages

The Bitcoin Cash Peer-to-Peer (P2P) Network protocol is a binary protocol used by Full Nodes and [SPV](/protocol/simple-payment-verification) Nodes, transmitted via TCP.  The P2P network is similar to a gossip network, where nodes listen for messages and then relays that message to its other peers if it believes that message's content is valid.

P2P network messages do not necessarily have a reply and there is no way to unambiguously connect a sent message to a reply, although many communications are often request/response pairs.  Nodes may handle incoming messages in parallel, so a message reply order cannot be assumed.  Messages that cannot be fulfilled are sometimes dropped with no reply, and sometimes replied to via a `reject` message.  

These design decisions were made with consideration to communication with untrusted/uncooperative partners.

*Developer Notes: A common message strategy is to wait for any message that provides the required data (with a timeout), and then separately issue the request in a retry loop to multiple peers.*

## Message Format

The P2P network has a variety of message types.  All P2P messages follow a binary format with the following structure:


| Field | Length | Format |
|--|--|--|
| net magic | 4 bytes | byte array<sup>[(BE)](/protocol/misc/endian/little)</sup> |
| command string | 12 bytes | string<sup>[(BE)](/protocol/misc/endian/little)</sup> |
| payload byte count | 4 bytes | integer<sup>[(LE)](/protocol/misc/endian/little)</sup> |
| payload checksum | 4 bytes | byte array<sup>[(LE)](/protocol/misc/endian/little)</sup> |
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

### Payload Byte Count

### Payload Checksum

### Payload