# Script

Bitcoin Cash transactions make use of a scripting language to authorize and secure transfers.
While, colloquially, there is a tendency to refer to transactions as "sending" Bitcoin Cash to "an address", that is merely an abstraction.
In fact, the only thing that permits the spending of existing [UTXOs](/protocol/blockchain/transaction#transaction-outputs) is the successful execution of a script.
The only thing preventing the spending of newly created UTXOs is the difficulty of producing a successfully executing script.
Through the use of cryptographic signatures and hash functions, such scripts are often designed specifically to be difficult to produce unless you are the intended spender of a given UTXO, though that need not necessarily be the case.

For more information on how Transactions are commonly secured, see [Locking Script](/protocol/blockchain/transaction/locking-script).

This page will focus on how the scripts are run, what they are capable of, and what limitations they have.

## Script Execution

Scripts are executed using a stack-based memory model and have an intentionally restricted set of available operations.  Unlike the common general-purpose programming languages your are probably aware of, Script (the term for the language itself) does not allow for loops, persistent state/memory across script executions, or the definition of functions.  Instead, scripts are expected to contain whatever data they need and use the available operations to prove transaction validity.

Scripts are run when validating transactions, and successful execution of all of the scripts defined by the transaction is a necessary, but not sufficient, condition for transaction validity.  See [Transaction Validation](/protocol/blockchain/transaction-validation) for more details.

As a part of validating a transaction, a script is built for each input spent by the transaction.  Each script is the concatenation of the [unlocking script](/protocol/blockchain/transaction/unlocking-script) provided with the input definition (which is used that the beginning of the script) and the locking script provided by the [previous output](/protocol/blockchain/transaction#transaction-outputs) being referenced (which is the end of the script).  The exception to this is [pay to script hash](/protocol/blockchain/transaction/locking-script#standard-scripts), which has an altered execution workflow.  In general, though, this combined unlocking/locking script is then executed and considered successful if and only if the following conditions are met:

 - **Non-Zero Value** - after execution the top of the stack must contain a non-zero (TRUE) value.
 - **No Stack Overflows** - no operation should attempt to pop a value from the stack when the stack is empty.
 - **Clean Stack** - after execution the stack must only contain a single value, which must be non-zero (TRUE).  Added in [HF20181115](/protocol/forks/HF20181115).

Additionally, in order for the combined script to be valid, the following must be true:

 - **Non-Empty Scripts** - both the locking and unlocking scripts must be non-empty.
 - **Max Script Length** - the locking and unlocking script must each be less than the max script length of 10,000 bytes (for a combined script maximum of 20,000 bytes).
 - **Contained Control Flow** - an IF/ELSE block cannot start in the unlocking script and end in the locking script, the script must be in the top-level scope when the locking script execution begins.
 - **Permitted Operations Only** - the locking script must not contain.
 - **Push Only** - the unlocking script must contain only push operations (i.e. those with op codes 0x60 or less).  Added in [HF20181115](/protocol/forks/HF20181115).

## Op Codes

The table below lists the currently allocated op codes.  Op codes marked with **(ignored)** are permitted but will do nothing when executed.  Op codes marked with **(disabled)** are permitted in scripts so long as they are not executed.  Op codes marked with **(do not use)** will make a transaction invalid merely be being present.

| Op Code Range | Name |
|--|--|
| 0x00 | [OP_0](/protocol/blockchain/script/op-codes/op-x), OP_FALSE |
| 0x01 - 0x4B | [OP_DATA_X](/protocol/blockchain/script/op-codes/op-data-x) |
| 0x4C | OP_PUSHDATA1 |
| 0x4D | OP_PUSHDATA2 |
| 0x4E | OP_PUSHDATA4 |
| 0x4F | OP_1NEGATE |
| 0x50 | OP_RESERVED **(disabled)** |
| 0x51 - 0x60 | [OP_1](/protocol/blockchain/script/op-codes/op-x) - OP_16, OP_TRUE |
| 0x61 | OP_NOP |
| 0x62 | OP_VER **(disabled)** |
| 0x63 | OP_IF |
| 0x64 | OP_NOTIF |
| 0x65 | OP_VERIF **(do not use)** |
| 0x66 | OP_VERNOTIF **(do not use)** |
| 0x67 | OP_ELSE |
| 0x68 | OP_ENDIF |
| 0x69 | OP_VERIFY |
| 0x6A | OP_RETURN |
| 0x6B | OP_TOALTSTACK |
| 0x6C | OP_FROMALTSTACK |
| 0x6D | OP_2DROP |
| 0x6E | OP_2DUP |
| 0x6F | OP_3DUP |
| 0x70 | OP_2OVER |
| 0x71 | OP_2ROT |
| 0x72 | OP_2SWAP |
| 0x73 | OP_IFDUP |
| 0x74 | OP_DEPTH |
| 0x75 | OP_DROP |
| 0x76 | OP_DUP |
| 0x77 | OP_NIP |
| 0x78 | OP_OVER |
| 0x79 | OP_PICK |
| 0x7A | OP_ROLL |
| 0x7B | OP_ROT |
| 0x7C | OP_SWAP |
| 0x7D | OP_TUCK |
| 0x7E | OP_CAT |
| 0x7F | OP_SUBSTR |
| 0x80 | OP_LEFT |
| 0x81 | OP_RIGHT |
| 0x82 | OP_SIZE |
| 0x83 | OP_INVERT |
| 0x84 | OP_AND |
| 0x85 | OP_OR |
| 0x86 | OP_XOR |
| 0x87 | OP_EQUAL |
| 0x88 | OP_EQUALVERIFY |
| 0x89 | OP_RESERVED1 **(do not use)** |
| 0x8A | OP_RESERVED2 **(do not use)** |
| 0x8B | OP_1ADD |
| 0x8C | OP_1SUB |
| 0x8D | OP_2MUL |
| 0x8E | OP_2DIV |
| 0x8F | OP_NEGATE |
| 0x90 | OP_ABS |
| 0x91 | OP_NOT |
| 0x92 | OP_0NOTEQUAL |
| 0x93 | OP_ADD |
| 0x94 | OP_SUB |
| 0x95 | OP_MUL |
| 0x96 | OP_DIV |
| 0x97 | OP_MOD |
| 0x98 | OP_LSHIFT |
| 0x99 | OP_RSHIFT |
| 0x9A | OP_BOOLAND |
| 0x9B | OP_BOOLOR |
| 0x9C | OP_NUMEQUAL |
| 0x9D | OP_NUMEQUALVERIFY |
| 0x9E | OP_NUMNOTEQUAL |
| 0x9F | OP_LESSTHAN |
| 0xA0 | OP_GREATERTHAN |
| 0xA1 | OP_LESSTHANOREQUAL |
| 0xA2 | OP_GREATERTHANOREQUAL |
| 0xA3 | OP_MIN |
| 0xA4 | OP_MAX |
| 0xA5 | OP_WITHIN |
| 0xA6 | OP_RIPEMD160 |
| 0xA7 | OP_SHA1 |
| 0xA8 | OP_SHA256 |
| 0xA9 | OP_HASH160 |
| 0xAA | OP_HASH256 |
| 0xAB | OP_CODESEPARATOR |
| 0xAC | OP_CHECKSIG |
| 0xAD | OP_CHECKSIGVERIFY |
| 0xAE | OP_CHECKMULTISIG |
| 0xAF | OP_CHECKMULTISIGVERIFY |
| 0xB0 | OP_NOP1 **(ignored)** |
| 0xB1 | OP_CHECKLOCKTIMEVERIFY |
| 0xB2 | OP_CHECKSEQUENCEVERIFY |
| 0xB3 | OP_NOP4 **(ignored)** |
| 0xB4 | OP_NOP5 **(ignored)** |
| 0xB5 | OP_NOP6 **(ignored)** |
| 0xB6 | OP_NOP7 **(ignored)** |
| 0xB7 | OP_NOP8 **(ignored)** |
| 0xB8 | OP_NOP9 **(ignored)** |
| 0xB9 | OP_NOP10 **(ignored)** |
| 0xBA | OP_CHECKDATASIG |
| 0xBB | OP_CHECKDATASIGVERIFY |
| 0xBC - 0xFF | Unused **(disabled)** |

### Node-Specific Behavior

Some node implementations define custom op codes.

#### bchd

| Op Code Range | Name |
|--|--|
| 0xFA | OP_SMALLINTEGER <img src="/_static_/images/warning.png" /> |
| 0xFB | OP_PUBKEYS <img src="/_static_/images/warning.png" /> |
| 0xFD | OP_PUBKEYHASH <img src="/_static_/images/warning.png" /> |
| 0xFE | OP_PUBKEY <img src="/_static_/images/warning.png" /> |
| 0xFF | OP_INVALIDOPCODE <img src="/_static_/images/warning.png" /> |

#### Bitcoin ABC

| Op Code Range | Name |
|--|--|
| 0xF0 | OP_PREFIX_BEGIN <img src="/_static_/images/warning.png" /> |
| 0xF7 | OP_PREFIX_END <img src="/_static_/images/warning.png" /> |
| 0xFF | OP_INVALIDOPCODE <img src="/_static_/images/warning.png" /> |

#### Bitcoin Unlimited

| Op Code Range | Name |
|--|--|
| 0xF0 | OP_BIGINTEGER <img src="/_static_/images/warning.png" /> |
| 0xF1 | OP_DATA <img src="/_static_/images/warning.png" /> |
| 0xFA | OP_SMALLINTEGER <img src="/_static_/images/warning.png" /> |
| 0xFB | OP_PUBKEYS <img src="/_static_/images/warning.png" /> |
| 0xFD | OP_PUBKEYHASH <img src="/_static_/images/warning.png" /> |
| 0xFE | OP_PUBKEY <img src="/_static_/images/warning.png" /> |
| 0xFF | OP_INVALIDOPCODE <img src="/_static_/images/warning.png" /> |

#### Flowee the Hub

| Op Code Range | Name |
|--|--|
| 0xFA | OP_SMALLINTEGER <img src="/_static_/images/warning.png" /> |
| 0xFB | OP_PUBKEYS <img src="/_static_/images/warning.png" /> |
| 0xFD | OP_PUBKEYHASH <img src="/_static_/images/warning.png" /> |
| 0xFE | OP_PUBKEY <img src="/_static_/images/warning.png" /> |
| 0xFF | OP_INVALIDOPCODE <img src="/_static_/images/warning.png" /> |