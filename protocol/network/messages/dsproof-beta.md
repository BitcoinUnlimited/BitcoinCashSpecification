# Announcement: Double Spend Proof ("dsproof-beta")

This message is meant to inform participants of attempts of double
spending an unconfirmed transaction by providing cryptographic provable
evidence that one UTXO entry was spent twice by the owner(s) of the funds.

## Summary

A double spend attack can be used, for instance, to redirect payments meant for a specific
merchant to a different target and thus defraud the merchant we want to pay to. The basic
concept of a double spend is that (at least) one unspent output is spent
twice in different transactions which forces miners to pick one of them to mine.

At its most basic we can detect this by finding two signed inputs both
spending the same output. In the case of pay-to-public-key-hash (P2PKH)
this means two signatures signing the same public key.

Cryptographic signatures in Bitcoin Cash follow the 'fork-id' algorithm described
[here](/protocol/forks/replay-protected-sighash),
which explains a change made to the Satoshi designed algorithm, a change after which the containing transaction itself is not signed, but a unique hash of that
transaction is being signed. This gives us the opportunity to send only
the intermediate hashes instead of the whole transaction while allowing
receivers to still validate both signatures of the same public
key. And therefore prove that a double spend has taken place.

## Network specification

A node that finds itself in possession of a correct double-spend-proof
shall notify its peers using the INV message, using a 'type' field with
number **0x94a0**. This will be changed to another number as this spec
is finalized.

The hash-ID for the double-spend-proof is a double sha256 over the entire
serialized content of the proof, as defined next.

In response to an INV any peer can issue a `getdata` message which will
cause a reply with the following message. The name of the message (until
this spec is finalized) is **`dsproof-beta`**.


| Field Size | Description | Data Type  | Comments |
| -----------|:-----------:| ----------:|---------:|
| 32 | TxInPrevHash | sha256 | The txid being spent |
| 4  | TxInPrevIndex | int | The output being spent |
| | FirstSpender | spender | the first sorted spender |
| | DoubleSpender | spender | the second spender |

A double-spend-proof essentially describes two inputs, both spending the
same output. As such the prev-hash and prev-index point to the output and
the spenders each describe inputs.

The details required to validate one input are provided in the spender field;

| Field Size | Description | Data Type  | Comments |
| -----------|:-----------:| ----------:|---------:|
| 4 | tx-version | unsigned int | Copy of the transactions version field |
| 4 | sequence | unsigned int | Copy of the sequence field of the input |
| 4 | locktime | unsigned int | Copy of the transactions locktime field |
| 32 | hash-prevoutputs | sha256 | Transaction hash of prevoutputs |
| 32 | hash-sequence | sha256 | Transaction hash of sequences |
| 32 | hash-outputs | sha256 | Transaction hash of outputs |
| 1-9 | list-size | var-int | Number of items in the push-data list |
|  | push-data | byte-array | Raw byte-array of a push-data. For instance a signature |

## Validation

It is required that nodes validate the proof before using it or forward it to other
nodes. Please check against the matching transaction in your mempool for
addresses so you can limit sending the proof only to interested nodes that
have registered a bloom filter.

Validation includes a short list of requirements;

1. The DSP message is well-formed and contains all fields. It is allowed (by
   nature of Bitcoin Cash) for some hashes to be all-zeros.
2. The two spenders are different, specifically the signature (push data)
   has to be different.
3. The first & double spenders are sorted with two hashes as keys.  
   Compare on the hash-outputs, and if those are equal, then compare on
   hash-prevoutputs.
   The sorting order is in numerically ascending order of the hash,
   interpreted as 256-bit little endian integers.
4. The double spent output is still available in the UTXO database,
   implying no spending transaction has been mined.
5. No other valid proof is known.

Further validation can be done by essentially validating the signature that
was copied from the inputs of both transactions against the output a node
should have in either its memory pool or its UTXO database.

To validate a spender of the proof, a node requires to have;

* The output being spent (mempool or UTXO)
* One of the transactions trying to spend the output.
* The double-spend-proof.

As the forkid
[specification](/protocol/forks/replay-protected-sighash)
details, the digest algorithm hashes 10 items in order to receive a sha256
hash, which is then signed.

These 10 items are;

1.  nVersion of the transaction (4-byte little endian)
2.  hashPrevouts (32-byte hash)
3.  hashSequence (32-byte hash)
4.  outpoint (32-byte hash + 4-byte little endian)
5.  scriptCode of the input (serialized as scripts inside CTxOuts)
6.  value of the output spent by this input (8-byte little endian)
7.  nSequence of the input (4-byte little endian)
8.  hashOutputs (32-byte hash)
9.  nLocktime of the transaction (4-byte little endian)
10. sighash type of the signature (4-byte little endian)

In the double spend message we include items: 1, 2, 3, 4, 7, 8, 9 and 10.

From the output we are trying to spend you can further obtain items: 5 & 6

The full transaction also spending the same output which you found in your
mempool, can be used to get the public key which you can use to validate
that the signature is actually correct.

When all rules are followed, the proof is valid.

## Limitation and risks

Not all types and all combinations of transactions are supported. Wallets
and point-of-sale applications are suggested to give a rating of how secure
an unconfirmed transaction is based on various factors.

Transactions that spend all, confirmed, P2PKH outputs with all inputs
signed SIGHASH\_ALL without ANYONECANPAY, are double-spend-proof's
"protected transactions".
