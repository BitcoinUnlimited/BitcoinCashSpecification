# OP_DATA_X

The OP_DATA_X op codes push a value to the top of the stack.  The value pushed to the stack follows this op code and is the length of the op code interpreted as a [two's_complement](https://en.wikipedia.org/wiki/Two%27s_complement) integer.  For example, OP_DATA_24 (op code 0x18) pushes the next 24 bytes in the script as a single value on the stack.