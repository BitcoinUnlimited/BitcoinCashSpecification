# Bitcoin Cash Protocol

### About

[Style Guide](/style-guide) — [Contributors](/contributors) — [Target Audience](/target-audience) — [Project History](/project-history)

### Basics
[Blockchain basics](/protocol/blockchain) — [Protocol hashing algorithms](/protocol/blockchain/hash) — Memory Pool

### Transactions
[Bitcoin Transaction](/protocol/blockchain/transaction) — [Unlocking Script](/protocol/blockchain/transaction/unlocking-script)— [Locking Script](/protocol/blockchain/transaction/locking-script)

### Blocks
[Bitcoin blocks](/protocol/blockchain/block) —
[Block header](/protocol/blockchain/block/block-header) — [Merkle Tree](/protocol/blockchain/block/merkle-tree) — [Transaction Ordering](/protocol/blockchain/block/transaction-ordering)

### Script (Bitcoin transaction language)
[Script](/protocol/blockchain/script) — [Operation codes (opcodes)](/protocol/blockchain/script#operation-codes-opcodes)

### Transaction validation
[Transaction Validation](/protocol/blockchain/transaction-validation) —
[Block-Level Validation Rules](/protocol/blockchain/transaction-validation/block-level-validation-rules) — [Network-Level Validation Rules](/protocol/blockchain/transaction-validation/network-level-validation-rules)

### Proof of Work (PoW)
[Proof of Work](/protocol/blockchain/proof-of-work) — [Difficulty Adjustment Algorithm](/protocol/blockchain/proof-of-work/difficulty-adjustment-algorithm) — [Mining](/protocol/blockchain/proof-of-work/mining) — Stratum Protocol — Mining Pools

### Addresses
Pay To Public Key (P2PK) — Pay To Public Key Hash (P2PKH) — Pay To Script Hash (P2SH) — Base 58 encoding (legacy) — Cashaddr encoding

### Cryptography
Secp256k1 — Public Key — Private Key — ECDSA signatures — Schnorr signatures — N-of-M multisig signatures

### Network upgrades
[Bip-16](/protocol/forks/bip-0016) — [Bip-34](/protocol/forks/bip-0034) — [Bip-37](/protocol/forks/bip-0037) — [Bip-64](/protocol/forks/bip-0064) — [Bip-65](/protocol/forks/bip-0065) — [Bip-66](/protocol/forks/bip-0066) — [Bip-68](/protocol/forks/bip-0068) — [Bip-112](/protocol/forks/bip-0112) — [Bip-113](/protocol/forks/bip-0113) — [Bip-157](/protocol/forks/bip-0157) — [Bip-158](/protocol/forks/bip-0158) — [Bip-159](/protocol/forks/bip-0159) — BCH-UAHF (BUIP-55) — [HF-20171113](/protocol/forks/hf-20171113) — HF-20180515 — HF-20181115 — HF-20190515 — HF-20191115

### Network protocol

[Network Messages](/protocol/network/messages) — [Handshake](/protocol/network/node-handshake)

#### Announcement messages

[filteradd](/protocol/network/messages/filteradd) — [filterclear](/protocol/network/messages/filterclear) — [filterload](/protocol/network/messages/filterload) — [inv](/protocol/network/messages/inv)

#### Request messages

feefilter — getaddr — [getblocks](/protocol/network/messages/getblocks) — [getdata](/protocol/network/messages/getdata) — [getheaders](/protocol/network/messages/getheaders) — [ping](/protocol/network/messages/ping) —
sendheaders — [version](/protocol/network/messages/version)

#### Response messages

[addr](/protocol/network/messages/addr) — block — headers — notfound — [merkleblock](/protocol/network/messages/merkleblock) — [pong](/protocol/network/messages/pong) —
[reject](/protocol/network/messages/reject) — tx — [verack](/protocol/network/messages/verack)

#### Other messages (extensions)

sendcmpct — get_xthin — xthinblock — thinblock — get_xblocktx — xblocktx —[xverack](/protocol/p2p/xverack) — [xversion](/protocol/p2p/xversion)

### Simple Payment Verification (SPV)
[Bloom Filters](/objects/bloom__filter)
### Simple Ledger Protocol
### Miscellaneous
[Endian](/protocol/misc/endian)
