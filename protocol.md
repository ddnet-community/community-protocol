## kaizocrown@m0rekz.github.io

**SENDER**: Server

**MESSAGE TYPE**: Game message

**UUID domain**: ``kaizocrown@m0rekz.github.io``

**UUID raw**: ``c4348e4a-18c3-3bc8-89db-8e0283e90b48``

**PAYLOAD**:

| Type | Name | Description |
| ---- | ---- | ----------- |
| Int | ClientId | A player client id that has a crown |

**IMPLEMENTATIONS**:

| Project | Details |
| ------- | ------- |
| [Kaizo Network](https://github.com/M0REKZ/kaizo-client/tree/discontinued-server) | Kaizo Network servers sends the crown for the player with the best time between all players online in the server, requires a NetMessage to be received indicating that the client has support for the message, so the server can send the crown |
| [Kaizo Client](https://github.com/M0REKZ/kaizo-client) | Renders a blue crown above indicated player character |

**DESCRIPTION**:

The server sends a crown each certain amount of ticks, indicating the player that is porting it.
While the client get the message, it should render a crown above the indicated player, if the message is not received in SERVER_TICK_SPEED ticks, the crown must be removed.

**EXAMPLE**:

```python
# datasrc/network.py
Messages = [
    # [..]
    	NetMessageEx("Sv_KaizoNetworkCrown", "kaizocrown@m0rekz.github.io", [
        NetIntAny("m_ClientId"),
      ]),
]
```

```C++
// send on the server side
if(Server()->Tick() % (Server()->TickSpeed()/5) == 0) // dont spam crown every tick
{
  CNetMsg_Sv_KaizoNetworkCrown CrownMsg;
  CrownMsg.m_ClientId = m_pPlayer->GetCid();
  Server()->SendPackMsg(&CrownMsg, MSGFLAG_VITAL, SnappingClient);
}
```

## kaizoversion@m0rekz.github.io

**SENDER**: Client

**MESSAGE TYPE**: System message

**UUID domain**: ``kaizoversion@m0rekz.github.io``

**UUID raw**: ``13d20a78-22fa-3d24-9882-91924665b4c9``

**PAYLOAD**:

| Type | Name | Description |
| ---- | ---- | ----------- |
| Int | Kaizo Network protocol version | Supported Kaizo Network protocol version |

**IMPLEMENTATIONS**:

| Project | Details |
| ------- | ------- |
| [Kaizo Network](https://github.com/M0REKZ/kaizo-client/tree/discontinued-server) | Kaizo Network needs this message to know they can send any other Kaizo-only message |
| [Kaizo Client](https://github.com/M0REKZ/kaizo-client) | Sends this message |

**DESCRIPTION**:

This message indicates the supported Kaizo Network protocol version (see [version_kz.h in Kaizo Client](https://github.com/M0REKZ/kaizo-client/blob/client/src/game/version_kz.h)), it is required to be sent in Kaizo Network servers to be able to receive Kaizo-only messages.

**EXAMPLE**:

```C++
// src/engine/shared/protocol_ex_msgs.h
UUID(NETMSG_KZ_KAIZO_NETWORK_VERSION, "kaizoversion@m0rekz.github.io")
```

```C++
// send on the client side
CMsgPacker Msg(NETMSG_KZ_KAIZO_NETWORK_VERSION, true);
Msg.AddInt(KAIZO_NETWORK_VERSION_LATEST);
SendMsg(Conn, &Msg, MSGFLAG_VITAL);
```

## cl-foxnet-fastinputs@foxnet-ddnet.github.io

**SENDER**: Client

**MESSAGE TYPE**: System message

**UUID domain**: ``cl-foxnet-fastinputs@foxnet-ddnet.github.io``

**UUID raw**: ``0031f379-9071-3694-8b35-cf402fba3292``

**PAYLOAD**:

| Type | Name | Description |
| ---- | ---- | ----------- |
| Int | Fast Inputs Enabled | Value indicating if Fast Inputs is enabled on this client |

**IMPLEMENTATIONS**:

| Project | Details |
| ------- | ------- |
| [FoxNet](https://github.com/FoxNet-DDNet/FoxNet) | Improves prediction for Fast Inputs if received with a True value |
| [Entity Client](https://github.com/FoxNet-DDNet/Entity-Client-DDNet/) | Sends this message if FoxNet info message is received |
| [Kaizo Client](https://github.com/M0REKZ/kaizo-client) | Sends this message if FoxNet info message is received |

**DESCRIPTION**:

Indicates if the client has Fast Inputs enabled, allows the server to give improved prediction info for the client.

**EXAMPLE**:

```C++
// src/engine/shared/protocol_ex_msgs.h
UUID(NETMSG_FOXNET_FASTINPUTS, "cl-foxnet-fastinputs@foxnet-ddnet.github.io")
```

```C++
// send on the client side
CMsgPacker Msg(NETMSG_FOXNET_FASTINPUTS, true);
Msg.AddInt(g_Config.m_TcFastInput);
SendMsg(Conn, &Msg, MSGFLAG_VITAL);
```
