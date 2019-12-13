A hash identifier is sequence of bytes derived from some source data that forms a probabilistically globally unique identifier that can be used to reference the source data.

Hash identifiers are calculated by running a cryptographically secure hash function on the source data.  Due to the properties of cryptographic hash functions, it is theoretically very hard (and in practice, impossible given current or future processing power) to find 2 different pieces of source data that form the same hash identifier in a reasonable time frame.  

This allows hash identifiers to be used in multiple applications, most importantly:

 - As a global "pointer" or "reference" to a piece of source data
 - As a "fingerprint" that identifies source data.
 - As a "commitment" that allows an entity to prove it knows some data at this time, without revealing that data until a later time. 

In Bitcoin, hash identifiers (sometimes just called a "hash" for short) are generally calculated using a double sha256 algorithm: SHA256(SHA256(source data)), resulting an a 32 byte hash identifier.  However, when a smaller hash is desirable and Wagner's Birthday attack cannot be deployed, bitcoin sometimes uses RIPEMD(SHA256(source data)) which results in a 20 byte hash identifier.  Notably, this is used in standard ([P2PKH](/glossary/p2pkh)) bitcoin addresses.