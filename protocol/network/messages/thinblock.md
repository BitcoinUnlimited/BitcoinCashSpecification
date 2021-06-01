# Response: THINBLOCK

This message delivers an `THINBLOCK` to the remote peer.
The `THINBLOCK` starts with the block header, followed by the hashes of all the transactions in the block.
The transaction hashes are in the same order as they are in the actual block.
All the transactions that are in the block, but not matched by the Bloom filter inlcuded in the previous [`GET_XTHIN`](get_xthin) mesaage should be appended, which should include at least the coinbase transaction.

## Format

| Field | Length | Format | Description |
|--|--|--|--|
| Block header | 80 bytes | [block header](..\blockchain\block\block-header) | The header of the block transmitted.|
| Transaction hashes | # of tx * 32 bytes | vector | The hashes of all the transactions included in the block.|
| Missing transactions | variable | vector | Transactions that are included in the blocks but are not matched by the Bloom filter.|
