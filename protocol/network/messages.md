# P2P Network Message

The Bitcoin Cash Peer-to-Peer (P2P) Network protocol is a binary protocol used by Full Nodes and [SPV](/protocol/simple-payment-verification) Nodes, transmitted over TCP/IP.
Individual nodes on the Bitcoin Cash network connect and create a mesh network where each node is indirectly connected to many others via just a couple of hops.
In the original Satoshi implementation of the P2P protocol the design of INV and getdata have been used for propagating transaction data using the rules of the gossip protocol values: forwarding validated transactions to a few peer-nodes who send it to others until the entire network has the transaction. This emergent behavior of the P2P layer allows fast propagation without undue strain on any individual node.

The P2P protocol is designed around messages. Each message is separate and self-contained. Nodes should be tolerant of message-types they do not understand. It is best to simply ignore those.
Detailed descriptions of the messages follows below. Generally speaking, each message is an event that the node can choose to respond to. Events range from notifications of new data (transactions/blocks/etc) and
actual requests for such data to be send and last the actual data being sent. Or, in some specific cases a `reject` message.

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
For Bitcoin Cash main net, the `net magic` field is always `0xE3E1F3E8`.
Any message received that does not begin with the `net magic` is invalid.

The `net magic` is designed to be unlikely to occur in normal data--the characters are rarely used upper ASCII, are not valid as UTF-8, and produce a large 32-bit integer with any alignment.
`0xE3E1F3E8` is the ASCII string, "cash", with each byte's highest bit set.

### Command String

The `command string` is a fixed-length 12 byte ASCII string.
Commands may not be longer than 12 bytes.
Commands that are shorter than 12 bytes are right-padded with null bytes (`0x00`).
The command string is used to determine the type of message being transmitted.
Messages with an unrecognized `command string` are ignored by most implementations but may result in a ban by implementations that diverge from the Satoshi-client defacto standard.

The following messages are considered standard by all node implementations.  
(TODO: the protocol is versioned, commands are introduced at certain versions, the previous line is ignoring this  
TODO: saying something is "standard" doesn't mean much in terms of spec. Is there an obligation to implement them?)

#### Announcements
| Command String | Synopsis | Supported Implementations
| -- | -- | -- |
| [filteradd](/protocol/p2p/filteradd) | *Adds a single item into an installed filter* | all
| [filterclear](/protocol/p2p/filterclear) | *Removes an installed filter* | all
| [filterload](/protocol/p2p/filterload) | *Inserts a transaction and merkle block filter into the peer* | all
| [inv](/protocol/p2p/inv) | *Notifies peers about the existence of some information (generally a block or transaction)* | all
  | [xupdate](/protocol/p2p/xupdate)  | *Communicates a change in peer capabilities* | BCHUnlimited

#### Requests
| Command String | Synopsis | Supported Implementations
| -- | -- | -- |
| feefilter |  |
| getaddr |  |
| [getblocks](/protocol/p2p/getblocks) | *Requests block hash identifiers* | all |
| [getdata](/protocol/p2p/getdata) | *Requests information from a peer* | all |
| [getheaders](/protocol/p2p/getheaders) | *Requests block headers from a peer*  | all |
| ping | [Ping](/protocol/network/messages/ping) | all |
| sendheaders |  |
| [version](/protocol/network/messages/version) | *Describes peer capabilities* | all
| [xversion](/protocol/p2p/xversion) | *Describes peer capabilities in an extensible manner* | BCHUnlimited


#### Responses
| Command String | Synopsis | Supported Implementations
| -- | -- | -- |
| [addr](/protocol/p2p/addr) | *Provides a peer with the addresses of other peers* | all
| block |  |
| [headers](/protocol/p2p/headers) | *Provides a set of block headers (unsolicited or GETHEADERS response)* | all |
| notfound |  |
|  [merkleblock](protocol/p2p/merkleblock) | *Provides a provable subset of a block's transactions, as filtered by FILTERADD*  | all |
| [Pong](/protocol/network/messages/pong) | *Reply to a ping message* | all |
| [reject](/protocol/p2p/reject) | *Response by well-behaved clients if a message cannot be handled*  | all
| [TX](/protocol/p2p/tx) | *Provide a transaction* | all
| [verack](/protocol/network/messages/verack) | *Respond to an [xversion](/protocol/p2p/xversion) message* | all

The following messages are well known, but not implemented by all node implementations.

| Command String | Synopsis | Supported Implementations
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

# Example message

# Node Specific Behavior

## Bitcoin Unlimited

### Payload Checksum

Bitcoin Unlimited does not validate the message checksum since messages are sent via TCP which has its own checksum paradigm.