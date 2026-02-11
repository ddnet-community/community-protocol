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
| [Kaizo Network](https://github.com/M0REKZ/kaizo-client/tree/discontinued-server) | Kaizo Network servers sends the crown for the player with the best time between all players online in the server, requires kaizoversion@m0rekz.github.io to be received indicating that the client has support for the message, so the server can send the crown |
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
// make sure the client supports the message
if(Server()->GetKaizoNetworkVersion(SnappingClient) >= KAIZO_NETWORK_VERSION_CROWNS && Server()->Tick() % (Server()->TickSpeed()/5) == 0) // dont spam crown every tick
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
| [Kaizo Network](https://github.com/M0REKZ/kaizo-client/tree/discontinued-server) | Kaizo Network needs this message to know it can send any other Kaizo-only message |
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
| [Kaizo Client](https://github.com/M0REKZ/kaizo-client) | Sends this message |

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

## game-data-prediction@netobj.teeworlds.wiki

**TYPE**: Snapshot net object

**UUID domain**: ``game-data-prediction@netobj.teeworlds.wiki``

**UUID raw**: ``c71bec48-83fa-3524-8145-686016f13009``

**PAYLOAD**:

| Type | Name | Description |
| ---- | ---- | ----------- |
| Int | Prediction Flag | A flag for server to disable some part of client prediction |

**IMPLEMENTATIONS**:
| Project | Details |
| ------- | ------- |
| [TeeworldsArchive](https://github.com/TeeworldsArchive/teeworlds) | Client-side local player event and input prediction could be disabled by server-side |

**DESCRIPTION**:

missing `GAMEPREDICTIONFLAG_EVENT` should only disable the prediction of local player's trigger events.

missing `GAMEPREDICTIONFLAG_INPUT` should disable the prediction of local player's trigger events and the prediction of local player input.

maybe you would ask why missing `GAMEPREDICTIONFLAG_INPUT` should disable the prediction of local player's trigger events, if we don't do that, then we couldn't get any trigger events of local player.

**EXAMPLE**:
```python
# datasrc/network.py
GamePredictionFlags = Flags("GAMEPREDICTIONFLAG", ["EVENT", "INPUT"])

Objects = [
    # [..]
    NetObjectEx("GameDataPrediction", "game-data-prediction@netobj.teeworlds.wiki", [
            NetFlag("m_PredictionFlags", GamePredictionFlags),
    ])
]
```

```C++
// this is the server side
CNetObj_GameDataPrediction *pGameDataPrediction = static_cast<CNetObj_GameDataPrediction *>(Server()->SnapNewItem(NETOBJTYPE_GAMEDATAPREDICTION, 0, sizeof(CNetObj_GameDataPrediction)));
if(!pGameDataPrediction)
	return;

pGameDataPrediction->m_PredictionFlags = GAMEPREDICTIONFLAG_EVENT | GAMEPREDICTIONFLAG_INPUT;

// As the client side is somewhat fragmented, please see https://github.com/TeeworldsArchive/teeworlds/pull/40
```

## kaizoplayerping@m0rekz.github.io

**SENDER**: Server

**MESSAGE TYPE**: Game Snap Object

**UUID domain**: ``kaizoplayerping@m0rekz.github.io``

**UUID raw**: ``d266e386-bae3-3be4-965a-0396e1e732dd``

**PAYLOAD**:

| Type | Name | Description |
| ---- | ---- | ----------- |
| Int | Ping | The Player's Ping, Client ID must be indicated at the Snap Object construction as indicated in the example below|

**IMPLEMENTATIONS**:

| Project | Details |
| ------- | ------- |
| [Kaizo Network](https://github.com/M0REKZ/kaizo-client/tree/discontinued-server) | Kaizo Network servers sends this message, requires kaizoversion@m0rekz.github.io to be received indicating that the client has support for the message, so the server can send the object |
| [Kaizo Client](https://github.com/M0REKZ/kaizo-client) | Renders a ping circle above indicated player character |

**DESCRIPTION**:

The server calculates ping and sends the snap object to all clients, implementing this Snap Object in new server mods is not recommended, since it uses space in the game Snapshot while this could had been implemented as a NetMessage.

**EXAMPLE**:

```python
# datasrc/network.py
Objects = [
    # [..]
    	NetObjectEx("KaizoNetworkPlayerPing", "kaizoplayerping@m0rekz.github.io", [
		NetIntAny("m_Ping", default=0),
	], validate_size=False),
]
```

```C++
// send on the server side
// Kaizo Network server uses LastAckedTick to calculate ping
// but you may want to use a different way
// also make sure to verify if the client supports the snap object
if(Server()->GetKaizoNetworkVersion(SnappingClient) >= KAIZO_NETWORK_VERSION_PLAYER_PING && m_LastAckedTick != -1)
	{
		CNetObj_KaizoNetworkPlayerPing *pKaizoPlayerPing = Server()->SnapNewItem<CNetObj_KaizoNetworkPlayerPing>(Id);
		if(!pKaizoPlayerPing)
			return;

		int diff = Server()->Tick() - m_LastAckedTick;

		if(diff > 50)
			diff = 50;

		pKaizoPlayerPing->m_Ping = (int)(diff * 1000/Server()->TickSpeed());
	}
```

```C++
// read on the client side
if(pItem->m_Type == NETOBJTYPE_KAIZONETWORKPLAYERPING)
    {
        const CNetObj_KaizoNetworkPlayerPing *pKaizoPlayerPing = (const CNetObj_KaizoNetworkPlayerPing *)pItem->m_pData;
        if(!pKaizoPlayerPing)
            return;
        
        int ClientId = pItem->m_Id;
        if(ClientId < 0 || ClientId >= MAX_CLIENTS)
            return;

        m_aClients[ClientId].m_ReceivedPing = pKaizoPlayerPing->m_Ping;
    }
```

## kaizopickup@m0rekz.github.io

**SENDER**: Server

**MESSAGE TYPE**: Game Snap Object

**UUID domain**: ``kaizopickup@m0rekz.github.io``

**UUID raw**: ``5c3b5a2a-f8a1-3347-baf9-8bda6fd31833``

**PAYLOAD**:

| Type | Name | Description |
| ---- | ---- | ----------- |
| Int | X | X Position |
| Int | Y | Y Position |
| Int | Type | Kaizo Network Weapon ID |
| Int | Switch | Switch Number |

**IMPLEMENTATIONS**:

| Project | Details |
| ------- | ------- |
| [Kaizo Network](https://github.com/M0REKZ/kaizo-client/tree/discontinued-server) | Kaizo Network sends this object if a Kaizo weapon is found on the map, if kaizoversion@m0rekz.github.io has not been received it will send a normal weapon object with a decoration (like a rotating laser) |
| [Kaizo Client](https://github.com/M0REKZ/kaizo-client) | If the Weapon ID is a Kaizo weapon, it will render the corresponding texture |

**DESCRIPTION**:

This object has information for the Kaizo weapon pickup found in the map, telling the position, type and switch ID for it.

``validate_size=False`` was added in case a future Kaizo Network version would add new information, but the original server mod is discontinued.
Use this object in your client if you want to display a custom texture for weapons in Kaizo-based servers (like TeeCloud).

Supported weapon IDs:
* KZ_CUSTOM_WEAPON_PORTAL_GUN = 6 (NUM_WEAPONS)
* KZ_CUSTOM_WEAPON_ATTRACTOR_BEAM = 7

**EXAMPLE**:

```C++
// datasrc/network.py
NetObjectEx("KaizoNetworkPickup", "kaizopickup@m0rekz.github.io", [
		NetIntAny("m_X", default=0),
        NetIntAny("m_Y", default=0),
        NetIntAny("m_Type", default=0),
        NetIntAny("m_Switch", default=0),
	], validate_size=False),
```

```C++
// send on the server side
if(Server()->GetKaizoNetworkVersion(SnappingClient) >= KAIZO_NETWORK_VERSION_PORTAL_ATTRACTOR)
{
	CNetObj_KaizoNetworkPickup *pPickup = Server()->SnapNewItem<CNetObj_KaizoNetworkPickup>(GetId());

	if(!pPickup)
		return;

	pPickup->m_X = (int)m_Pos.x;
	pPickup->m_Y = (int)m_Pos.y;
	pPickup->m_Type = m_Subtype;
	pPickup->m_Switch = m_Number;
}
```
