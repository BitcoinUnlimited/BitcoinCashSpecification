The block header appears in several P2P messages.  It's serialization format is as follows:

| 4 bytes | 32 bytes | 32 bytes | 4 bytes | 4 bytes | 4 bytes
|----|---------------|-----------|------------|---------|------
| version |  hash of previous block  |  hash of merkle root  |  block time in epoch seconds  |  difficulty in "bits" format  |  nonce