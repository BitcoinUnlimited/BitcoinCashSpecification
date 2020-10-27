# Response: XTHINBLOCK

This message delivers an `XTHINBLOCK` to the remote peer. The `XTHINBLOCK` starts with the block header, followed by the truncated hashes of all the transactions in the block. The transaction hashes are truncated to the first 64 bits and are in the same order as they are in the actual block. All the transactions that are in the block, but not matched by the Bloom filter inlcuded in the previous [`GET_XTHIN`](get_xthin) mesaage should be appended, which should include at least the coinbase transaction.  

## Format

| Field | Length | Format | Description |
|--|--|--|--|
| Block header | 80 bytes | [block header](..\blockchain\block\block-header) | The header of the block transmitted. |  
| Transaction hashes | # of tx * 8 bytes | vector | The truncated hashes of all the transactions included in the block. |  
| Missing transactions | variable | vector | Transactions that are included in the blocks but are not matched by the Bloom filter. |