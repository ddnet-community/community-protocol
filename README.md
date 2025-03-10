# community-protocol

Unofficial ddnet protocol extension messages.

[ddnet](https://github.com/ddnet/ddnet) has an extensible network protocol where custom domains can be used as message identifiers.
This repository is an effort to standardize custom messages. So different custom clients and servers can agree on how to communicate.

Some messages are too custom to land in ddnet but too generic to be reinvented and bound to every projects specific domain.

These messages can be bound to the **ddnet-community.github.io** domain.

This applies to:
- [game messages](https://github.com/ddnet/ddnet/blob/31b8807ab14efa21d025771f74ef3ced2c41c5ac/datasrc/network.py#L540-L542)
- [system messages](https://github.com/ddnet/ddnet/blob/31b8807ab14efa21d025771f74ef3ced2c41c5ac/src/engine/shared/protocol_ex_msgs.h)
- [snap items](https://github.com/ddnet/ddnet/blob/31b8807ab14efa21d025771f74ef3ced2c41c5ac/datasrc/network.py#L247-L263)

# projects using the community-protocol

- None lol
