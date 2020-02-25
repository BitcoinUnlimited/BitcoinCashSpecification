<div class="cwikmeta">{
"title":"GETDATA",
"related":["/protocol","/protocol/p2p/inv"]
}</div>

# Request: Get Data (“getdata”)

Requests information (generally previously announced via an INV) from a peer.

A GETDATA request is a [vector](/protocol/p2p/vector.md) of INV-formatted data.

## Message Format

| Field | Length | Format | Description |
|--|--|--|--|
| vector length N | variable | compact int | number of items |
| item 0 type | 4 bytes | unsigned int<sup>[(LE)](/protocol/misc/endian/little)</sup> | type of the requested object |
| item 0 hash | 32 bytes | bytes | hash of the requested object |
| ... | | | |
| item N-1 type |
| item N-1 hash 


### Type

The type of the desired object. See [INV](/protocol/network/messages/inv.md) for specific values

### Hash
The [hash identifier](glossary/hash__identifier) of the desired object.

## Server Implementations 

[Bitcoin Unlimited](https://github.com/BitcoinUnlimited/BitcoinUnlimited/blob/bucash1.7.0.0/src/net_processing.cpp#L1021)

## Client Implementations