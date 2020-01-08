
# Handshake: Version (“version”)

The version message is a part of the node connection [handshake](/protocol/network/node-handshake) and indicates various connection settings, networking information, and the services provided by the sending node (see Services Bitmask [below](#services-bitmask)).

The node connection is not considered established until both nodes have sent and received both a <code>version</code> and [verack](/protocol/network/messages/verack) message.

## Message Format

| Field | Length | Format | Description |
|--|--|--|--|
| version number | 4 bytes | unsigned integer<sup>[(LE)](/protocol/misc/endian/little)</sup> | The version number supported by the sending node. |
| services | 8 bytes | bitmask<sup>[(LE)](/protocol/misc/endian/little)</sup> | An indication of the services supported by the sending node.  See Services Bitmask section below. |
| timestamp | 8 bytes | unix timestamp<sup>[(LE)](/protocol/misc/endian/little)</sup> | The time the message was generated on the sending node. |
| remote address | 26 bytes | [network address](/protocol/formats/network-address) | The network address of the remote node.  <p>_NOTE: this does not contain the timestamp normally included with network addresses._</p> |
| local address | 26 bytes | [network address](/protocol/formats/network-address) | The network address of the sending node. <p>_NOTE: this does not contain the timestamp normally included with network addresses._</p> |
| nonce | 8 bytes | bytes<sup>[(LE)](/protocol/misc/endian/little)</sup> | Random nonce for the connection, used to detect connections to self. |
| user agent | variable | [variable length string](/protocol/formats/variable-length-string) | A user agent string identifying the node implementation. |
| block height | 4 bytes | unsigned integer<sup>[(LE)](/protocol/misc/endian/little)</sup> | The height of the block with the highest height known to the sending node. |
| relay flag | 1 byte | boolean | Indicates whether the sending node would like all broadcasted transactions relayed to it.  See [BIP-37](/protocol/forks/bip-0037).  This flag is sometimes referred to as "fRelay". |

Note: appending extra data after the `relay flag` is ignored.  Historically, extra data after the `relay flag` would sometimes result in the connection being banned, although this is no longer standard behavior.

## Version Number

The most recent version of the network protocol is `70015`.  The `version` value often correlates to new behavior, parsing formats, and available services; for more details review the network protocol's [version history](/history/protocol-version).  Nodes should use `version` and the `services` bitmask to determine if the node should accept the incoming connection.  Related: [node connection handshake](/protocol/network/node-handshake).

## Services Bitmask
The services field is an 8 byte little-endian-serialized bitfield that described peer capabilities.  The following capabilities are defined, by bit position:

### Standard Services
* 0: NODE_NETWORK
	The node is capable of serving the complete block chain. It is currently set by all full nodes, and is unset by SPV clients or other peers that just want network services but don't provide them.

* 2: NODE_BLOOM 
	The node is capable and willing to handle bloom-filtered connections.

* 3: NODE_WITNESS
	Indicates that a node can be asked for blocks and transactions including witness data.  
	*Bitcoin Cash nodes do not have witness data so this flag should be ignored on receipt and set to 0 when sent*

* 5: NODE_BITCOIN_CASH 
	The node supports the BCH chain.  This is intended to be just a temporary service bit until the BTC/BCH fork actually happens.

* 24-31: Reserved for experimental changes
	These bits are reserved for temporary experiments. Just pick a bit that isn't getting used, or one not being used much, and notify the community. Remember that service bits are just unauthenticated advertisements, so your code must be robust against collisions and other cases where nodes may be advertising a service they do not actually support.

## Example Serialized Data

Net Magic<sup>[(BE)](/protocol/misc/endian/little)</sup>
`E3E1F3E8`

Command String ("version")<sup>[(BE)](/protocol/misc/endian/big)</sup>
`76657273696F6E0000000000`

Payload Byte Count<sup>[(LE)](/protocol/misc/endian/little)</sup>
`6A000000`

Payload Checksum<sup>[(LE)](/protocol/network/messages/message-checksum)</sup>
`8FC7709F`

Version Number
`7F110100`

Node Features
`3500000000000000`

**Timestamp** *("1576101548")*
`AC66F15D00000000`

**Remote Address** *("5.6.7.8:8333")*
`240000000000000000000000000000000000FFFF0506070820`

**Local Address** *("1.2.3.4:8333")*
`8D350000000000000000000000000000000000FFFF01020304`

**Nonce**
`208D00F0E6495B9B`

**User Agent** *("/Bitcoin Node:1.2.3/")*
`4350142F426974636F696E204E6F64653A312E322E332F`

**Current Block Height** *("612918L")*
`365A0900`

**Relay Transactions Flag** *("true")*
`01`


### Node Specific Messages

#### Bitcoin ABC

* 10: NODE_NETWORK_LIMITED <img src="/_static_/images/warning.png" />
	This means the same as NODE_NETWORK with the limitation of only serving a small subset of the blockchain.  See [BIP159](/protocol/forks/bip-0159) for details on how this is implemented.

#### Bitcoin Unlimited

* 4: NODE_XTHIN  <img src="/_static_/images/warning.png" />
	The node supports Xtreme Thinblocks

* 6: NODE_GRAPHENE <img src="/_static_/images/warning.png" />
	The node supports Graphene blocks.  If this is turned off then the node will not service graphene requests nor make graphene requests.

* 10: NODE_NETWORK_LIMITED <img src="/_static_/images/warning.png" />
	This means the same as NODE_NETWORK with the limitation of only serving a small subset of the blockchain.  See [BIP159](/protocol/forks/bip-0159) for details on how this is implemented.

#### Bitcoin Verde

* 7: BLOCKCHAIN_INDEX_ENABLED <img src="/_static_/images/warning.png" />
	Indicates that the node is an indexing node and supports returning information custom to the requesting user's addresses.

* 8: SLP_INDEX_ENABLED <img src="/_static_/images/warning.png" />
	Indicates that the node tracks Simple Ledger Protocol validity and supports returning this status for individual transactions.

#### Other Proposed/Previously Used Service Flags

* 1: NODE_GETUTXO <img src="/_static_/images/warning.png" />
The node is capable of responding to the getutxo protocol request. See [BIP 64](/protocol/forks/bip-0064) for details on how this is implemented. _Was previously supported by Bitcoin XT only._

* 7: NODE_WEAKBLOCKS <img src="/_static_/images/warning.png" />
	The node supports Storm weak block (currently no node supports these in production, so this is a placeholder).

* 8: NODE_CF <img src="/_static_/images/warning.png" />
	Indicates the node is capable of serving compact block filters to SPV clients, AKA the "Neutrino" protocol ([BIP157](/protocol/forks/bip-0157), and [BIP158](/protocol/forks/bip-0158)).

## Node-Specific Behavior

Generally, though node implementations may be aware of services they do not provide, they generally ignore those they don't supported.  Any notable deviations from that behavior are documented below.

### Bitcoin ABC

Bitcoin ABC nodes may, once they have reached their maximum number of peers, selectively disconnect from nodes that do not supported "desired services", though it appears currently this just <code>NODE_NETWORK</code> and/or <code>NODE_NETWORK_LIMITED</code>.  That is, it may prefer nodes that store and serve blocks.

### Bitcoin Verde

Bitcoin Verde nodes ignores any data after the `relay flag` field.