Vectors (and arrays, lists, maps, etc) are serialized by first serializing the number of elements and then serializing each element.  The number of elements is serialized in [compact int](/protocol/p2p/compact__int) format.

| vector element count | object #0 | object #1 | ... | object #count-1 |
|-------------|--------------|-------------|----------------|------------|
| [compact int](/protocol/p2p/compact__int) | object specific serialization | ... | ... | ... |
|