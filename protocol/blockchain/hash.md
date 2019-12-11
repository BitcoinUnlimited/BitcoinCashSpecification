# Hashes

A variety of hashing algorithms are used throughout the Bitcoin Cash protocol.  In this specification we will not get into what hashes are, nor the details of the specific hashing algorithms used, as that is covered well elsewhere.  Instead, this page will focus on which hashing algorithms are used, where they are used, and why they are used there.

 ## SHA-256
 
[SHA-256]([https://en.wikipedia.org/wiki/SHA-2](https://en.wikipedia.org/wiki/SHA-2)) is widely used throughout the Bitcoin Cash protocol to identify blocks and transactions along with a variety of purposes in transaction scripts.  The most notable uses of SHA-256 are:

 - Block Hashing (Double SHA-256)
	 - A SHA-256 hash is taken of the block header.  The output hash is then hashed again with SHA-256.  This resultant hash is referred to simply as the block hash and is used as a unique identifier for the block.
	 - This double hash removes the possibility of a [length extension attack](https://en.wikipedia.org/wiki/Length_extension_attack) which a single SHA-256 is vulnerable to.  While this is generally not a problem for Bitcoin Cash since the pre-image (the actual data of the block) is available, it trades a minor amount of inefficiency for confidence that this property of SHA-256 cannot be exploited.
 - Transaction Hashing
	 - Transactions are hashed using a single application of SHA-256.  This is referred to as the transaction hash and is used to uniquely identify the transaction.  (NOTE: Historical transaction hashes are not universally unique two sets of two identical coinbase transactions, thus repeated hashes.  Since the block height is now required to be in the coinbase transaction, this should not be possible in the future.)
	 - The two cases where this occurred are the transactions with the hashes <code>D5D27987D2A3DFC724E359870C6644B40E497BDC0589A033220FE15429D88599</code> and <code>E3BF3D07D4B0375638D5F1DB5255FE07BA2C4CB067CD81B84EE974B6585FB468</code>.

 ## RIPEMD-160
 
[RIPEMD-160](https://en.wikipedia.org/wiki/RIPEMD) is used in Bitcoin Cash scripts to create short, quasi-anonymous representations of payees for transactions.  Since its brevity is also a potential liability for the anonymity it provides (since shorter hashes generally provide less collision-resistance), it is used in conjunction with SHA-256 when generating an address from a public key.  That is, <code>(public key) -> SHA-256 -> RIPEMD-160 -> (address)</code>.
 
 ## Murmur

[MurmurHash](https://en.wikipedia.org/wiki/MurmurHash) is used in Bitcoin to support [Bloom filters](https://en.wikipedia.org/wiki/Bloom_filter).  The specific version used is the MurmurHash version 3 (32-bit), with the first hash initialized to <code>(numberOfHashesRequired * 0xFBA4C795L + nonce)</code> where <code>nonce</code> is a randomly chosen 32-bit unsigned integer.