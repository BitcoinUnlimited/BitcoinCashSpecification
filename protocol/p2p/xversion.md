<div class="cwikmeta">{
"title":"XVERSION",
"related":["/protocol","/protocol/p2p/xupdate"]
}</div>

*Notifies peers about a protocol configuration value*

This message notifies a peer about extended protocol parameters.  This message MAY be sent during connection initialization.  If sent, it MUST be sent immediately subsequent to the receipt of the [VERACK](/protocol/p2p/verack.md) message, and before other non-initialization messages are sent.

| compact int | compact int | variable bytes |... | compact int | variable bytes |
|----------|---------|----------|---|---------|----------| 
|[vector](/protocol/p2p/vector) size N of|   key 1  | value 1 [vector](/protocol/p2p/vector) of bytes  | | key N | value N [vector](/protocol/p2p/vector) of bytes

The *value* is a vector of bytes.  These bytes can be an object that is itself serialized, but **MUST** exist within the vector "envelope" so that implementations that do not recognize a field can skip it.   The serialization format of the bytes inside the "envelope" is defined by the creator of the key, however, Bitcoin P2P network serialization is recommended since it is also used to encode/decode all the messages of this protocol.

See [XVERSION specification](https://github.com/BitcoinUnlimited/BitcoinUnlimited/blob/bucash1.7.0.0/doc/xversionmessage.md) for additional details.

### XVERSION Fields

XVERSION field identifiers are 32 bits and split into a 16 bit prefix and 16 bit suffix.  Each development group is assigned a prefix so that new identifiers do not accidentally conflict.  Once a field identifier is created by group A, it should be used by other software both to receive that information from A and to present that information to other software.  Therefore, group A **MUST NOT** change the syntax or semantics of a field once defined.  To change a field, create a new identifier and deprecate (by no longer using the original identifier).

#### Prefix Assignments
|    Group          | Value |
|-------------------|-------|
| Bitcoin ABC       |   0   |
| Bitcoin Unlimited |   2   |


#### Field Assignments

See [xversionkeys.dat](https://github.com/BitcoinUnlimited/BitcoinUnlimited/blob/bucash1.7.0.0/src/xversionkeys.dat) for the most up-to-date field definitions defined by the BitcoinUnlimited full node.
Note that:
* *u64c* refers to a [compact int](/protocol/p2p/compact__int.md) serialization of an unsigned 64-bit value.
* *Changeable* fields MAY be changed during the course of a connection via the [XUPDATE](/protocol/p2p/xupdate) message.

### Support

Supported by: **Bitcoin Unlimited**