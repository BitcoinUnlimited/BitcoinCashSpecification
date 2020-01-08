# Network Protocol Version History

| Version Number | Proposed In | Summary |
|--|--|--|
| 106 | ??? | Added the following fields to the `version` message: `address-from`, `nonce`, `user-agent`, `current block height` |
| 209 | ??? | `address` message may accept a list of network addresses. |
|31 402| ??? | Time field added to `address` messages. |
|  | [BIP-0014](/history/bips) | Network Version decoupled from Block Version.  User-agent replaced sub-version number. |
| 60 000 | [BIP-0031](/history/bips) | Added pong. |
| 70 001 | [BIP-0037](/history/bips) | Added `relay` flag to the `version` message.|