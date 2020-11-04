
The services field is an 8 byte little-endian-serialized bitfield that described peer capabilities.  The following capabilities are defined, by bit position:

* 0: NODE_NETWORK
	The node is capable of serving the complete block chain. It is currently set by all full nodes, and is unset by SPV clients or other peers that just want network services but don't provide them.

 * 1: NODE_GETUTXO
	 The node is capable of responding to the getutxo protocol request.  See [BIP 64](https://github.com/bitcoin/bips/blob/master/bip-0064.mediawiki) for details on how this is implemented.  *Supported by Bitcoin XT only*

* 2: NODE_BLOOM 
	The node is capable and willing to handle bloom-filtered connections.

* 3: NODE_WITNESS
	Indicates that a node can be asked for blocks and transactions including witness data.  
	*Bitcoin Cash nodes do not have witness data so this flag should be ignored on receipt and set to 0 when sent*


* 4: NODE_XTHIN 
	The node supports Xtreme Thinblocks
	*Supported by Bitcoin Unlimited only*

* 5: NODE_BITCOIN_CASH 
	The node supports the BCH chain.  This is intended to be just a temporary service bit until the BTC/BCH fork actually happens.

* 6: NODE_GRAPHENE
	The node supports Graphene blocks.  If this is turned off then the node will not service graphene requests nor make graphene requests.
	*Supported by Bitcoin Unlimited only*

* 7: NODE_WEAKBLOCKS
	The node supports Storm weak block (currently no node supports these in production, so this is a placeholder).

* 8: NODE_CF 
	Indicates the node is capable of serving compact block filters to SPV clients, AKA the "Neutrino" protocol ([BIP157](https://github.com/bitcoin/bips/blob/master/bip-0157.mediawiki), and [BIP158](https://github.com/bitcoin/bips/blob/master/bip-0158.mediawiki)).

* 9: NODE_NETWORK_LIMITED 
	This means the same as NODE_NETWORK with the limitation of only serving a small subset of the blockchain.  See [BIP159](https://github.com/bitcoin/bips/blob/master/bip-0159.mediawiki) for details on how this is implemented.

* 24-31: Experimental 
	These bits are reserved for temporary experiments. Just pick a bit that isn't getting used, or one not being used much, and notify the community. Remember that service bits are just unauthenticated advertisements, so your code must be robust against collisions and other cases where nodes may be advertising a service they do not actually support.

