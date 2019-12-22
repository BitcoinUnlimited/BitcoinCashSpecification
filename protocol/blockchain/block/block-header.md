# Block Header

Block headers serve an important intermediary role in the creation and transmission of blocks.
They are a fixed-width (80-byte) representation of the entire block.
With a block header, you can:

 1. Calculate the hash of the block.
	 - The double SHA-256 hash of the block header.
 2. Confirm the proof of work was executed correctly.
	 - See [Difficulty Adjustment Algorithm](/protocol/blockchain/proof-of-work/difficulty-adjustment-algorithm) for more details.
 3. Determine the relative location of the block in the blockchain.
	 - Using the previous block hash contained in the header

Since validation of all the transactions in the block can be expensive, the ability to perform these checks on the block before downloading and validating its transactions helps make denial-of-service attacks on the network significantly more expensive for attackers.

## Block Header Format

| Field | Length | Format | Description |
|--|--|--|--|
| version | 4 bytes | unsigned integer<sup>[(LE)](/protocol/misc/endian/little)</sup> | The block format version. |
| previous block hash | 32 bytes | [block hash](/protocol/blockchain/hash)<sup>[(LE)](/protocol/misc/endian/little)</sup> | The hash of the block immediately preceding this block in the blockchain. |
| merkle root | 32 bytes | [merkle root](/protocol/blockchain/merkle-tree)<sup>[(LE)](/protocol/misc/endian/little)</sup> | The merkle tree root of the transactions in the block. |
| timestamp | 4 bytes | unix timestamp<sup>[(LE)](/protocol/misc/endian/little)</sup> | The epoch timestamp of the block in seconds. |
| difficulty target | 4 bytes | [difficulty target](#difficulty-target-encoding)<sup>[(LE)](/protocol/misc/endian/little)</sup> | The difficulty that the block must achieve to be valid.  This value is determined by the timestamps of previously mined blocks. |
| nonce | 4 bytes | bytes<sup>[(LE)](/protocol/misc/endian/little)</sup> | A random value that is repeatedly changes during block mining in order to achieve the block hash requirements. |

### Difficulty Target Encoding

Within the block header, the difficulty target uses a special floating-point representation that helps keep the size of the block header small.
While the [Difficulty Adjustment Algorithm](/protocol/blockchain/proof-of-work/difficulty-adjustment-algorithm) attempts to calculate the ideal target (i.e. the value the block hash must be "less than"), it undergoes a lossy conversion when put in the block header:

| Field | Length | Format | Description |
|--|--|--|--|
| exponent | 1 byte | byte | Used to calculate the offset for the signficand.  The actual exponent is `8 * (exponent - 3)`. |
| significand | 3 byte | bytes | The significand, or mantissa, of the value. |

Ultimately, the difficulty target is equal to:
<pre>significand * 2<sup>(8 * (exponent - 3))</sup></pre>