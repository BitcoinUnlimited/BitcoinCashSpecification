<div class="cwikmeta">{
"title":"INV",
"related":["/protocol","/protocol/p2p/filterclear", "/protocol/p2p/inv", "/protocol/p2p/MERKLEBLOCK"]
}</div>


*Inserts a transaction and merkle block filter into the peer*

| up to 36000 bytes |
|-------------------|
|   [bloom filter](objects/bloom_filter)   |

### Effect on Transactions

This message installs a bloom filter into the peer.  Subsequent INV notifications and MERKLEBLOCK messages only provide transactions that in match this bloom filter in some manner.  The following items in a transaction are checked against the bloom filter:

 - The transaction hash
 - Each data field in every [output script](glossary/output__script) in the transaction
	 - Most importantly, this allows public keys and public key hashes (essentially bitcoin addresses) to be added to the bloom filter, allowing a wallet to detect an incoming transfer.
 - Each [previous output](glossary/previous__output) in the transaction
	 - This allows a wallet to detect that a different wallet has spent funds that are co-controlled
 - Each data field in every [input script](glossary/input__script) in the transaction.

See [CBloomFilter::MatchAndInsertOutputs](https://github.com/BitcoinUnlimited/BitcoinUnlimited/blob/bucash1.7.0.0/src/bloom.cpp#L186), and [CBloomFilter::MatchInputs](https://github.com/BitcoinUnlimited/BitcoinUnlimited/blob/eb264e627e231f7219e60eef41b4e37cc52d6d9d/src/bloom.cpp#L234)

### Effect on Merkle Blocks

If a [filtered block](https://github.com/BitcoinUnlimited/BitcoinUnlimited/blob/bucash1.7.0.0/src/protocol.h#L483) is requested via in [INV](/protocols/p2p/inv) message, the installed bloom filter is used to choose which transactions are included in the response using the same matching algorithm as described above for transaction INVs.
