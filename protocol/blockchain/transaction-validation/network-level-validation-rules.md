# Network-Level Validation Rules

In some cases, transactions may be rejected by the network despite the fact that they successfully unlock their inputs, spend valid UTXOs, and do not conflict with other transactions.
That is, a node may receive the transaction and consider it valid, but it may choose not to relay it to its peers or reject it outright.
In particular, custom, or non-standard, transactions are often treated this way by the Bitcoin Cash network at large.
Custom transactions are defined as those which are not considered standard.

## Standard Transactions

Standard transactions are those that:

 - Only have outputs that use [standard locking scripts](/protocol/blockchain/transaction/locking-script#standard-scripts)
 - Are below the maximum transaction size of 100,000 bytes
 - Have a valid version number (currently only version 2 is valid)
 - Have input scripts that only contain push operations
 - Have input scripts with unlocking scripts below the 1650 byte maximum
 - Have at most one [data output](/protocol/blockchain/transaction/locking-script#data-output)
 - For [multisig](/protocol/blockchain/transaction/locking-script#multisig) outputs, must have at most 3 parties and at least 1 required party (i.e. 1-of-1 through 3-of-3).
 - Have non-data outputs with amount above the [dust](#dust) threshold

Be aware, however, that these rules may vary from node-to-node as they are often configurable.
Some nodes may also accept and relay non-standard transactions.
For this reason, among others, it is always wise to send transactions to multiple nodes.

### Dust

In order to limit the propagation of transactions with limited utility, outputs that would be cost-prohibitive to spend are rejected as "dust."
Dust is defined differently for different node implementations but in the simplest cases a threshold of 546 satoshis is used.
Outputs with fewer satoshis than the dust threshold are rejected, along with the transaction they are a part of.
The exception to this is provably unspendable outputs (e.g. data outputs), which always have a dust threshold of zero satoshis.

## Node-Specific Behavior

### bchd

bchd provides the following comment regarding its dust calculation:

    The output is considered dust if the cost to the network to spend the
    coins is more than 1/3 of the minimum free transaction relay fee.
    minFreeTxRelayFee is in Satoshi/KB, so multiply by 1000 to
    convert to bytes.
    
    Using the typical values for a pay-to-pubkey-hash transaction from
    the breakdown above and the default minimum free transaction relay
    fee of 1000, this equates to values less than 546 satoshi being
    considered dust.

### Bitcoin ABC

Bitcoin ABC provides the following description of its dust threshold calculation:

    "Dust" is defined in terms of dustRelayFee, which has units
    satoshis-per-kilobyte. If you'd pay more than 1/3 in fees to spend
    something, then we consider it dust.  A typical spendable txout is 34
    bytes big, and will need a CTxIn of at least 148 bytes to spend: so dust
    is a spendable txout less than 546*dustRelayFee/1000 (in satoshis).

### Bitcoin Unlimited

Bitcoin Unlimited uses a static threshold of 546 satoshis (except provably non-spendable outputs which are zero).

### Bitcoin Verde

Bitcoin Verde performs a similar calculation to Bitcoin ABC but with two differences:

 1. If the address used is uncompressed, the input size is increase to 180 bytes.
 2. The output length used is always 34 bytes, instead of serializing the output.

This is accompanied by the comment:

> For the common default _satoshisPerByteFee (1), the dust threshold is 546 satoshis.