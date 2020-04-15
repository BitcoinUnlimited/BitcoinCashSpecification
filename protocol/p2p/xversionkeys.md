<div class="cwikmeta">{
"title":"XVERSIONKEYS",
"related":["/protocol","/protocol/p2p/xversion","/protocol/p2p/xupdate"]
}</div>


### Prefix Assignments
|    Group                | Value |
|-------------------------|-------|
| Reserved for versioning |   0   |
| Bitcoin Cash Node       |   1   |
| Bitcoin Unlimited       |   2   |


#### Field Assignments

See [xversionkeys.dat](https://github.com/BitcoinUnlimited/BitcoinUnlimited/blob/bucash1.7.0.0/src/xversionkeys.dat) for the most up-to-date field definitions defined by the BitcoinUnlimited full node.
Note that:
* *u64c* refers to a [compact int](/protocol/p2p/compact__int.md) serialization of an unsigned 64-bit value.
* *Changeable* fields MAY be changed during the course of a connection via the [XUPDATE](/protocol/p2p/xupdate) message.

### Support

Supported by: **Bitcoin Unlimited**
