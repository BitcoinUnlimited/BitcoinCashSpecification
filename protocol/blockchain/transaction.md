# Transaction

A transaction is how transfers are made in the blockchain. It comprises of a set of [transaction inputs](#transaction-inputs) which will be spent to a set of [transaction outputs](#transaction-outputs). The blockchain mining and full node software ensures that every transaction follows the blockchain's rules before admitting the transaction into a block. Verification of a transaction ensures that the inputs have not already been spent, that the total number of satoshis, the smallest quantity of Bitcoin Cash, provided as input is greater than or equal to the number of satoshis output (any extra is given to the miner as a transaction fee), and that the transaction is syntactically and cryptographically valid.

## Format

| Field | Length | Format | Description |
|--|--|--|--|
| version | 4 bytes | unsigned integer<sup>[(LE)](/protocol/misc/endian/little)</sup> | The version of the transaction format.  Currently `0x02000000`. |
| input count | variable | [variable length integer](/protocol/formats/variable-length-integer) | The number of inputs in the transaction. |
| transaction inputs | variable | `input count` [transaction inputs](#transaction-inputs) | Each of the transaction's inputs serialized in order. |
| output count | variable | [variable length integer](/protocol/formats/variable-length-integer) | The number of output in the transaction. |
| transaction outputs | variable | `output count` [transaction outputs](#transaction-outputs) | Each of the transaction's outputs serialized in order. |
| lock time | 4 bytes | unsigned integer<sup>[(LE)](/protocol/misc/endian/little)</sup> | The block height or timestamp after which this transaction is allowed to be included in a block.  If less than `500,000,000`, this is interpreted as a block height.  If more than `500,000,000`, this is interpreted as a unix timestamp in seconds.  Ignored if all of the transaction input sequence numbers are `0xFFFFFFFF`.<br/><br/>Note that at 10 minutes per block, it will take over 9,500 years to reach block height 500,000,000.  Also note that when Bitcoin was created the unix timestamp was well over 1,000,000,000. |

## Transaction Inputs

Transaction inputs are the "debits" of Bitcoin Cash and not only designate the satoshis that will be transferred as a part of the transaction, but they also provide proof of ownership via the [unlocking script](/protocol/blockchain/transaction/unlocking-script).  A transaction input references a unspent transaction output, or UTXO, from a prior transaction.  If a transaction would like to spend multiple UTXOs, it must have multiple inputs, one for each UTXO.

### Format

| Field | Length | Format | Description |
|--|--|--|--|
| previous output transaction hash | 32 bytes | [transaction hash](/protocol/blockchain/hash) | The hash of the transaction containing the output to be spent. |
| output index | 4 bytes | unsigned integer<sup>[(LE)](/protocol/misc/endian/little)</sup> | The zero-based index of the output to spent in the previous output's transaction. |
| unlocking script length | variable | [variable length integer](/protocol/formats/variable-length-integer) | The size of the unlocking script in bytes. |
| unlocking script | variable | bytes<sup>[(BE)](/protocol/misc/endian/big)</sup> | The contents of the unlocking script. |

## Transaction Outputs

Transaction outputs are the "credits" of Bitcoin Cash.  Each transaction output denotes a number of satoshis and the requirements are spending them.  These requirements take the form of a [locking script](/protocol/blockchain/transaction/locking-script) and can equate to anything from the satoshis only being spendable by the owner of a specific private key, to anyone being able to spend them, to no one being able to spend them.  While a transaction output has not been spent by another transaction (i.e. had its locking script "unlocked" by an input of a valid transaction) it is referred to as an unspent transaction output, or UTXO.  After the output has been spent, it is referred to as a previous output, or PrevOut.

### Format

| Field | Length | Format | Description |
|--|--|--|--|
| value | 8 bytes | unsigned integer<sup>[(LE)](/protocol/misc/endian/little)</sup> | The number of satoshis to be transferred. |
| locking script length | variable | [variable length integer](/protocol/formats/variable-length-integer) | The size of the unlocking script in bytes. |
| locking script | variable | bytes<sup>[(BE)](/protocol/misc/endian/big)</sup> | The contents of the locking script. |
