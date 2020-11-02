# Request: Filter Fee (“filterfee”)

Requests that the recipient withhold transactions that provide less than the given threshold of fees, in satoshis per byte.

The recipient node may, but is not required to, begin to perform this filtering and send only transactions that have fees at or above this threshold to the sender.

## Message Format

| Field | Length | Format | Description |  
|--|--|--|--|
|  minimum fee per byte  | 8 bytes | unsigned 64 bit integer<sup>[(LE)](/protocol/misc/endian/little)</sup> | The minimum number of satoshis per byte in fees desired by the sender.