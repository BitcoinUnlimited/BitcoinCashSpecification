<div class="cwikmeta">
{
"title":"P2P Protocol",
"related":[]
} </div>

The Bitcoin P2P protocol is comprised of messages over TCP.  These messages are serialized using a custom format.  Unlike RPC protocols, messages do not necessarily have a reply and there is no way to unambiguously connect a sent message to a reply, although many communications are often request/response pairs.   High performance full node software may handle incoming messages in parallel, so it is not appropriate to assume a message reply order.  Messages that cannot be fulfilled are sometimes dropped with no reply, and sometimes replied to via a REJECT message.

These legacy design decisions can make the protocol difficult to implement on the client side, but are generally needed when a robust implementation communicates with untrusted/uncooperative partners.  A good strategy is to wait for any message that provides the required data, with a timeout, and then separately issue the request in a retry loop to multiple peers.  If a timeout occurs, return to higher level software which should re-assess whether the data is still needed, since in nodes in the network may have converged to a competing block or transaction, and therefore not be serving the data you are requesting (nodes only serve data on the most-difficult chain, even if they have some data pertaining to lower-difficulty splits).

## Serialization Format

Bitcoin uses a custom serialization format that is generally little-endian.


## Message Envelope

The following table describes the message header:

| network identifier | command | size | checksum | contents |
|-------------|--------------|-------------|----------------|------------|
| 0: 4 bytes | 4: 12 bytes | 16: 4 bytes | 20: 4 bytes | 24: size bytes |
|*BCH*:E3,E1,F3,E8<br>*BTC*:F9,BE,B4,D9<br>*tBCH*:F4,E5,F3,F4<BR>*tBTC*:0B,11,09,07 | ascii null extended | little endian uint32 | little endian uint32 | depends on command

### Network Identifier
The network identifier is used to separate blockchains and test networks.  This reduces unnecessary load on peers, allowing them to rapidly ban nodes rather then forcing the peer to do a blockchain analysis before banning.  

Unfortunately, the BCH and BSV blockchains use the same network identifier.

### Command
The command is the exact lowercase bytes in the titles of each subsection in the Message Types section below followed by zeros -- e.g. the INV message's command is literally the 12 bytes: 'i','n',v',0,0,0,0,0,0,0,0,0.  This is not a "C" string.  If a command was exactly 12 bytes, there would be no null terminator!

### Size
Size is the size of the contents field in bytes, not the size of the entire message.

### Checksum
This field is a message checksum.  The checksum is calculated by first computing the double SHA256 of the *contents* portion of the message.  The first 4 bytes of this hash is the checksum. See [C++ generate](https://github.com/BitcoinUnlimited/BitcoinUnlimited/blob/bucash1.7.0.0/src/net.cpp#L3179), [python verify](https://github.com/BitcoinUnlimited/BitcoinUnlimited/blob/eb264e627e231f7219e60eef41b4e37cc52d6d9d/qa/rpc-tests/test_framework/mininode.py#L409), [python generate](https://github.com/BitcoinUnlimited/BitcoinUnlimited/blob/eb264e627e231f7219e60eef41b4e37cc52d6d9d/qa/rpc-tests/test_framework/mininode.py#L449).

Since TCP has message integrity checksums, and a peer can cause another node to waste processing power validating bad checksums, it is not recommended that nodes verify this checksum.  That is, this field should be considered obsolete.

However, senders should calculate and fill this checksum field to be compatible with all software, unless the [XVERSION](/protocol/p2p/xversion.md) message is used to tell peers that this checksum will not be verified (currently supported by *Bitcoin Unlimited*).  In this case the field **SHOULD** be set to 0 (but not enforced as 0 on the receiver's side).  This may allow a future reuse of this field.


### Contents
The contents of messages are described in the next section.

## Message Contents

### Announcements (unsolicited messages with no response)
#### [FILTERADD](/protocol/p2p/filteradd)
*Add a single item into an existing filter*

#### [FILTERCLEAR](/protocol/p2p/filterclear)
*Remove an existing filter*

#### [FILTERLOAD](/protocol/p2p/filterload)
*Inserts a transaction and merkle block filter into the peer*

#### [INV](protocol/p2p/inv)
*Notifies peers about the existence of some information (generally a block or transaction)*

#### [XUPDATE](/protocol/p2p/xupdate)
*Communicates a change in peer capabilities*

### Requests

#### [GETBLOCKS](/protocol/p2p/getblocks)
*Requests block hash identifiers*

#### [GETDATA](/protocol/p2p/getdata)
*Requests information (generally previously announced via an INV) from a peer*

#### [GETHEADERS](/protocol/p2p/getheaders)
*Requests block headers from a peer*

#### [PING](/protocol/p2p/ping)
*Keep-alive*

#### [VERSION](/protocol/p2p/version)
*Describes peer capabilities*

#### [XVERSION](/protocol/p2p/xversion)
*Describes peer capabilities in an extensible manner*
*Currently supported by Bitcoin Unlimited only*

#### Responses
Note that some of these "response" messages can also be sent without solicitation (i.e. without a request).

#### [ADDR](/protocol/p2p/addr)
*Provides a peer with the addresses of other peers*

#### BLOCK, THINBLOCK, XTHINBLOCK, GRAPHENEBLOCK, CMPCTBLOCK
*Provides a block*

#### [HEADERS](/protocol/p2p/headers)
*Provides a set of block headers (unsolicited or GETHEADERS response)*


#### [MERKLEBLOCK](protocol/p2p/merkleblock)
*Provides a provable subset of a block's transactions, as filtered by FILTERADD*

#### [PONG](/protocol/p2p/pong)
*Reply to a ping message*

#### [REJECT](/protocol/p2p/reject)
*General response by well-behaved clients if a message cannot be handled*


#### [TX](/protocol/p2p/tx)
*Transaction object*

#### [VERACK](/protocol/p2p/verack)
*Respond to a [version](/protocol/p2p/version) message*

#### [XVERACK](/protocol/p2p/xverack)
*Respond to an [xversion](/protocol/p2p/xversion) message*