# Script

Bitcoin Cash transactions make use of a scripting language to authorize and secure transfers.
While, colloquially, there is a tendency to refer to transactions as "sending" Bitcoin Cash to "an address", that is merely an abstraction.
In fact, the only thing that permits the spending of existing [UTXOs](/protocol/blockchain/transaction#transaction-output) is the successful execution of a script.
The only thing preventing the spending of newly created UTXOs is the difficulty of producing a successfully executing script.
Through the use of cryptographic signatures and hash functions, such scripts are often designed specifically to be difficult to produce unless you are the intended spender of a given UTXO, though that need not necessarily be the case.

For more information on how Transactions are commonly secured, see [Locking Script](/protocol/blockchain/transaction/locking-script).

This page will focus on how the scripts are run, what they are capable of, and what limitations they have.

## Script Execution

Scripts are executed using a stack-based memory model and have an intentionally restricted set of available operations.
Unlike the common general-purpose programming languages your are probably aware of, Script (the term for the language itself) does not allow for loops, persistent state/memory across script executions, or the definition of functions.
Instead, scripts are expected to contain whatever data they need and use the available operations to prove transaction validity.

### Features

In addition to the primary stack ("the stack"), there is a secondary stack, referred to as the "alt-stack", which data can be moved to temporarily.
Any data left on the alt-stack is lost when a given sub-script finishes execution.
In effect, any data moved to the alt-stack by an unlocking script is not present when the locking script runs.

There are a large number of op-codes that support everything from simple stack-manipulation, to mathematical calculations, to complex cryptographic processes.  In terms of control structures there are only basic conditional branching (IF/ELSE) operations available.

### Transaction Validation

Scripts are run when validating transactions, and successful execution of all of the scripts defined by the transaction is a necessary, but not sufficient, condition for transaction validity.
See [Transaction Validation](/protocol/blockchain/transaction-validation) for more details.

As a part of validating a transaction, a script is built for each input spent by the transaction.
Each script is the sequential execution (carrying over the same stack, but not alt-stack) of the [unlocking script](/protocol/blockchain/transaction/unlocking-script) provided with the input definition (which is used that the beginning of the script) and the locking script provided by the [previous output](/protocol/blockchain/transaction#transaction-output) being referenced.
The exception to this is [pay to script hash](/protocol/blockchain/transaction/locking-script#standard-scripts), which has an altered execution workflow.
In general, though, this combined unlocking/locking script is then executed and considered successful if and only if the following conditions are met:

 - **Non-Zero Value** - after execution the top of the stack must contain a non-zero (TRUE) value.
 - **No Stack Overflows** - no operation should attempt to pop a value from the stack when the stack is empty.  An overflow of the alt-stack is also disallowed.
 - **Clean Stack** - after execution the stack must only contain a single value, which must be non-zero (TRUE).  Added in [HF-20181115](/protocol/forks/hf-20181115).  The alt-stack is exempt from this.

Additionally, in order for the combined script to be valid, the following must be true:

 - **Non-Empty Scripts** - both the locking and unlocking scripts must be non-empty.
 - **Max Script Length** - the locking and unlocking script must each be less than the max script length of 10,000 bytes (for a combined script maximum of 20,000 bytes).
 - **Contained Control Flow** - an IF/ELSE block cannot start in the unlocking script and end in the locking script, the script must be in the top-level scope when the locking script execution begins.
 - **Permitted Operations Only** - the locking script must not include operations that are disallowed and must not execute operations that are disabled..
 - **Push Only** - the unlocking script must contain only push operations (i.e. those with op codes 0x60 or less).  Added in [HF-20181115](/protocol/forks/hf-20181115).

## Operation codes (opcodes)

The table below lists the currently allocated op codes.  Op codes marked with **(ignored)** are permitted but will do nothing when executed.
Op codes marked with **(disabled)** are permitted in scripts so long as they are not executed.
Op codes marked with **(do not use)** are disallowed and will make a transaction invalid merely be being present.

### Constants

| Word            | Value | Hex       | Input | Output | Description                                                 |
| --------------- | ----- | --------- | ----- | ------ | ----------------------------------------------------------- |
| OP_0, OP_FALSE  | 0     | 0x00      |       | 0      | An empty array of bytes is pushed onto the stack. See also [OP_X](/protocol/blockchain/script/op-codes/op-x) |
| N/A             | 1-75  | 0x01-0x4b |       |        | The next *value* bytes is data to be pushed onto the stack. See also [OP_DATA_X](/protocol/blockchain/script/op-codes/op-data-x) |
| OP_PUSHDATA1    | 76    | 0x4c      |       |        | The next byte contains the number of bytes to be pushed onto the stack.  |
| OP_PUSHDATA2    | 77    | 0x4d      |       |        | The next two bytes contain the number of bytes to be pushed onto the stack in little endian order. |
| OP_PUSHDATA4    | 78    | 0x4e      |       |        | The next four bytes contain the number of bytes to be pushed onto the stack in little endian order. |
| OP_1NEGATE      | 79    | 0x4f      |       | -1     | The number -1 is pushed onto the stack.                     |
| OP_1, OP_TRUE   | 81    | 0x51      |       | 1      | The number 1 is pushed onto the stack.                      |
| OP_2-OP_16      | 82-96 | 0x52-0x60 |       | 2-16   | The number (2-16) is pushed onto the stack.                 |

### Flow control

### Stack

### Splice

|Word       |Value  |Hex |Input         |Output  | Description                                                      |
|-----------|-------|----|--------------|--------|------------------------------------------------------------------|
|OP_CAT     |126    |0x7e|x1 x2         |out     |Concatenates two byte sequences                                   |
|OP_SPLIT   |127    |0x7f|x n           |x1 x2   |Splits byte sequence *x* at position *n*. Known as OP_SUBSTR before 2018-05-15. |
|OP_NUM2BIN |128    |0x80|a b           |out     |Converts numeric value *a* into byte sequence of length *b*. Known as OP_LEFT before 2018-05-15.       |
|OP_BIN2NUM |129    |0x81|x             |out     |Converts byte sequence *x* into a numeric value. Known as OP_RIGHT before 2018-05-15. |
|OP_SIZE     |130    |0x82|x         |x size     |Pushes the string length of the top element of the stack (without popping it). |

### Bitwise logic

|Word       |Value  |Hex |Input         |Output  | Description                                                      |
|-----------|-------|----|--------------|--------|------------------------------------------------------------------|
|OP_INVERT      |131    |0x83|N/A           |N/A              | **DISABLED**                                           |
|OP_AND         |132    |0x84|x1 x2         |out              |Boolean *AND* between each bit of the inputs            |
|OP_OR          |133    |0x85|x1 x2         |out              |Boolean *OR* between each bit of the inputs.            |
|OP_XOR         |134    |0x86|x1 x2         |out              |Boolean *EXCLUSIVE OR* between each bit of the inputs.  |
|OP_EQUAL       |135    |0x87|x1 x2         |true / false     |Returns 1 if the inputs are exactly equal, 0 otherwise. |
|OP_EQUALVERIFY |136    |0x88|x1 x2         |Nothing / *fail* |Same as OP_EQUAL, but runs OP_VERIFY afterward.         |

### Arithmetic

|Word       |Value  |Hex |Input         |Output  | Description                                                      |
|-----------|-------|----|--------------|--------|------------------------------------------------------------------|
|OP_DIV     |150    |0x96|a b           |out     |*a* is divided by *b*                                             |
|OP_MOD     |151    |0x97|a b           |out     |return the remainder after *a* is divided by *b*                  |

### Cryptography

|Word       |Value  |Hex |Input         |Output  | Description                                                      |
|-----------|-------|----|--------------|--------|------------------------------------------------------------------|
| OP_CHECKDATASIG | 186 | 0xba | sig msg pubkey | true / false | <!-- TODO --> |
| OP_CHECKDATASIGVERIFY | 187 | 0xbb | sig msg pubkey | nothing / *fail* | Same as OP_CHECKDATASIG, but runs OP_VERIFY afterward. |


### Locktime

| Word                   | Value   | Hex       | Input |Output     | Description                                     |
| ---------------------- | ------- | --------- | ----- | --------- | ----------------------------------------------- |
| OP_CHECKLOCKTIMEVERIFY | 177     | 0xb1      | x     |x / *fail* | Marks transaction as invalid if the top stack item is greater than the transaction's nLockTime field, otherwise script evaluation continues as though an OP_NOP was executed. Transaction is also invalid if 1. the stack is empty; or 2. the top stack item is negative; or 3. the top stack item is greater than or equal to 500000000 while the transaction's nLockTime field is less than 500000000, or vice versa; or 4. the input's nSequence field is equal to 0xffffffff. The precise semantics are described in BIP65. |
| OP_CHECKSEQUENCEVERIFY | 178     | 0xb2      | x     |x / *fail* |  Marks transaction as invalid if the relative lock time of the input (enforced by BIP68 with nSequence) is not equal to or longer than the value of the top stack item. The precise semantics are described in BIP112. |

### Reserved

| Word             | Value   | Hex       | Description                                                              |
| ---------------- | ------- | --------- | ------------------------------------------------------------------------ |
| OP_NOP1          | 176     | 0xb0      | Previously reserved for OP_EVAL (BIP12).                                 |
| OP_NOP4-OP_NOP10 | 179-185 | 0b3-0xb9  | Ignored. Does not mark transaction as invalid.                           |

### Uncategorized

Please help improve this article by catigorizing and describing the following up codes.

| Hex  | Word |
| ---- | ---- |
| 0x50 | OP_RESERVED **(disabled)** |
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
