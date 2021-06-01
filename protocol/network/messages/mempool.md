# Request: Mempool ("mempool")

Requests that the recipient notify the sender of transactions that are currently in its [mempool](/protocol/blockchain/memory-pool).
Recipients of a `mempool` message MAY respond with a set of transaction hashes currently in their mempool via an [`inv`](/protocol/network/messages/inv) message.

## Message Format
This message has no contents.

