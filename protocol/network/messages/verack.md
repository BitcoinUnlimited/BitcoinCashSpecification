# Handshake: Acknowledge ("verack")

The `verack` message is sent in reply to a [version](/protocol/network/messages/version) message.
Sending a `verack` in response to a `version` message indicates to the remote that its connection and version has been accepted.

There is no version negotiation functionality between nodes; therefore if the node does not accept the version supplied by the remote then the node disconnects instead of responding with a `verack`.

This `verack` message consists of only a message header; the command string is "verack".

## Example Serialized Data

Net Magic<sup>[(BE)](/protocol/misc/endian/little)</sup>
`E3E1F3E8`

Command String ("verack")<sup>[(BE)](/protocol/misc/endian/big)</sup>
`76657261636B000000000000`

Payload Byte Count<sup>[(LE)](/protocol/misc/endian/little)</sup>
`00000000`

Payload Checksum<sup>[(LE)](/protocol/network/messages/message-checksum)</sup>
`5DF6E0E2`