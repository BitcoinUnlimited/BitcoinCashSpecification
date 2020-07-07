A "compact int" is serialized as follows:

*If the number < 253, store it in 1 byte
*If the number fits in 16 bits: store a 1 byte value 253, and the 2 byte little-endian number.

|    0   |     1   |    2    | 
|--------|---------|---------|
|  0xfd  | val&255 | val>>8  |

* If the number fits in 32 bits (but not 8 or 16): store a 1 byte value 254, and the 4 byte little-endian number

|   0    |    1    |    2    |     3   |    4    | 
|--------|---------|---------|---------|---------|
|  0xfe  | val&255 | (val>>8)&255  | (val>>16)&255 | (val>>24)&255 |

* If the number fits in 64 bits (but not 8, 16, or 32): store a 1 byte value 255 and the 8 byte little-endian number
 
|    0   |    1    |    2    |     3   |     4   |    5    |   6      |     7   |    8    |
|--------|---------|---------|---------|---------|---------|---------|---------|--------|
|  0xff  | val&255 | val>>8  | val>>16 | val>>24 | val>>32 | val>>40  | val>>48 | val>>56 |