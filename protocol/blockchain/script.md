# Script

## Script Execution

## Operation codes (opcodes)

This is the list of Bitcoin Cash Script opcodes. 

See https://en.bitcoin.it/wiki/Script. See also https://github.com/bitcoincashorg/bitcoincash.org/blob/3d86e3f6a8726ebbe2076a96e3d58d9d6e18b0f4/spec/may-2018-reenabled-opcodes.md and https://github.com/bitcoincashorg/bitcoincash.org/blob/3d86e3f6a8726ebbe2076a96e3d58d9d6e18b0f4/spec/op_checkdatasig.md

### Constants

| Word            | Value | Hex       | Input | Output | Description                                                 | 
| --------------- | ----- | --------- | ----- | ------ | ----------------------------------------------------------- |
| OP_0, OP_FALSE  | 0     | 0x00      |       | 0      | An empty array of bytes is pushed onto the stack.           |
| N/A             | 1-75  | 0x01-0x4b |       |        | The next *value* bytes is data to be pushed onto the stack. |
| OP_PUSHDATA1    | 76    | 0x4c      |       |        | The next byte contains the number of bytes to be pushed onto the stack.  |
| OP_PUSHDATA2    | 77    | 0x4d      |       |        | The next two bytes contain the number of bytes to be pushed onto the stack in little endian order. |
| OP_PUSHDATA4    | 78    | 0x4e      |       |        | The next four bytes contain the number of bytes to be pushed onto the stack in little endian order. |
| OP_1NEGATE      | 79    | 0x4f      |       | -1     | The number -1 is pushed onto the stack.                     |
| OP_1, OP_TRUE   | 81    | 0x51      |       | 1      | The number 1 is pushed onto the stack.                      |
| OP_2-OP_16      | 82-96 | 0x52-0x60 |       | 2-16   | The number (2-16) is pushed onto the stack.                 |

### Flow control

<!-- TODO -->

### Stack

<!-- TODO -->

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

<!-- TODO -->

### Cryptography

|Word       |Value  |Hex |Input         |Output  | Description                                                      |
|-----------|-------|----|--------------|--------|------------------------------------------------------------------|
| OP_CHECKDATASIG | 186 | 0xba | sig msg pubkey | true / false | <!-- TODO --> |
| OP_CHECKDATASIGVERIFY | 187 | 0xbb | sig msg pubkey | nothing / *fail* | Same as OP_CHECKDATASIG, but runs OP_VERIFY afterward. |

<!-- TODO -->

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

