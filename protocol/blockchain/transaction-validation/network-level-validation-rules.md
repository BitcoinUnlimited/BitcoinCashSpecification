# Network-Level Validation Rules

In some cases, transactions may be rejected by the network despite the fact that they successfully unlock their inputs, spend valid UTXOs, and do not conflict with other transactions.  That is, a node may receive the transaction and consider it valid, but it may choose not to relay it to its peers or reject it outright.  In particular, custom, or non-standard, transactions are often treated this way by the Bitcoin Cash network at large.  Custom transactions are defined as those which are not considered standard.

## Standard Transactions

Standard transactions are those that:

 - Only have outputs that use [standard locking scripts](/protocol/blockchain/transaction/locking-script#Standard%20Scripts)
 - Are below the maximum transaction size of 100,000 bytes
 - Have a valid version number (currently only version 2 is valid)
 - Have input scripts that only contain push operations
 - Have input scripts with unlocking scripts below the 1650 byte maximum
 - Have at most one data (OP_RETURN) output
 - Have non-data outputs with amount above the [dust](#dust) threshold

Be aware, however, that these rules may vary from node-to-node as they are often configurable.  Some nodes may also accept and relay non-standard transactions.  For this reason, among others, it is always wise to send transactions to many nodes.

### Dust