# P2P Network Message

The Bitcoin Cash Peer-to-Peer (P2P) Network protocol is a binary protocol used by Full Nodes and [SPV](/protocol/simple-payment-verification) Nodes, transmitted via TCP.
The P2P network is similar to a gossip network, where nodes listen for messages and then relays that message to its other peers if it believes that message's content is valid.

P2P network messages do not necessarily have a reply and there is no way to unambiguously connect a sent message to a reply, although many communications are often request/response pairs.
Nodes may handle incoming messages in parallel, so a message reply order cannot be assumed.
Messages that cannot be fulfilled are sometimes dropped with no reply, and sometimes replied to via a `reject` message.  

These design decisions were made with consideration to communication with untrusted/uncooperative partners.

*Developer Notes: A common message strategy is to wait for any message that provides the required data (with a timeout), and then separately issue the request in a retry loop to multiple peers.*

## Message Format

The P2P network has a variety of message types.
All P2P messages follow a binary format with the following structure:


| Field | Length | Format |
|--|--|--|
| net magic | 4 bytes | byte array<sup>[(BE)](/protocol/misc/endian/big)</sup> |
| command string | 12 bytes | string<sup>[(BE)](/protocol/misc/endian/big)</sup> |
| payload byte count | 4 bytes | integer<sup>[(LE)](/protocol/misc/endian/little)</sup> |
| payload checksum | 4 bytes | byte array<sup>[(BE)](/protocol/misc/endian/big)</sup> |
| payload | variable |  |

### Net Magic

The network identifier is used to separate blockchains and test networks.
This reduces unnecessary load on peers, allowing them to rapidly ban nodes rather then forcing the peer to do a blockchain analysis before banning or disconnecting.
For Bitcoin Cash main net, the `net magic` field is always `E3E1F3E8`.
Any message received that does not begin with the `net magic` is invalid.

The `net magic` is designed to be unlikely to occur in normal data--the characters are rarely used upper ASCII, are not valid as UTF-8, and produce a large 32-bit integer with any alignment.
`E3E1F3E8` is the ASCII string, "cash", with each byte's highest bit set.

### Command String

The `command string` is a fixed-length 12 byte ASCII string.
Commands may not be longer than 12 bytes.
Commands that are shorter than 12-bytes are right-padded with null bytes (`0x00`).
The command string is used to determine the type of message being transmitted.
Messages with an unrecognized `command string` are ignored.

The following messages are considered standard by all node implementations.

#### Announcements
| Command String | Name |
| -- | -- |
| filteradd |  |
| filterclear |  |
| filterload |  |
| inv |  |

#### Requests
| Command String | Name |
| -- | -- |
| feefilter |  |
| getaddr |  |
| getblocks |  |
| getdata |  |
| getheaders |  |
| ping | [Ping](/protocol/network/messages/ping) |
| sendheaders |  |
| version | [Handshake: Version](/protocol/network/messages/version) |


#### Responses
| Command String | Name |
| -- | -- |
| addr |  |
| block |  |
| headers |  |
| notfound |  |
| merkleblock |  |
| pong | [Pong](/protocol/network/messages/pong) |
| reject |  |
| tx |  |
| verack | [Handshake: Acknowledge Version](/protocol/network/messages/verack) |

The following messages are well known, but not implemented by all node implementations.

| Command String | Name | Supported Implementations |
| -- | -- | -- |
| get_xblocktx |  |  |
| get_xthin |  |  |
| mempool |  |
| sendcmpct |  |  |
| thinblock |  |  |
| xblocktx |  |  |
| xthinblock |  |  |

### Payload Byte Count

The payload byte count is the size of the payload, encoded as a [little-endian](/protocol/misc/endian/little) 4-byte integer.
The total max size of any message is `268,435,456` bytes (256 MiB), and the header for a message is always 24 bytes, therefore the max value of the payload byte count is `268,435,432` bytes.
The payload byte count may be zero, but must not be negative.

### Payload Checksum

The message checksum is the first 4 bytes of a double-sha256 hash of the payload.
The checksum is transmitted as a byte array, and is encoded as [big-endian](/protocol/misc/endian/big).


### Payload

The message payload is defined by the message type.

# Node Specific Behavior

## Bitcoin Unlimited

### Payload Checksum

Bitcoin Unlimited does not validate the message checksum since messages are sent via TCP which has its own checksum paradigm.