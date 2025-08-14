## language@ddnet-community.github.io

**SENDER**: Client

**MESSAGE TYPE**: Game message

**UUID domain**: ``language@ddnet-community.github.io``

**UUID raw**: ``cdacd897-b421-3600-a1bb-98f7deacec9b``

**PAYLOAD**:

| Type | Name | Description |
| ---- | ---- | ----------- |
| String | Langugage | [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639_language_codes) language code for examaple: ``en`` for english and ``de`` for german |

**IMPLEMENTATIONS**:

| Project | Details |
| ------- | ------- |
| | |

**DESCRIPTION**:

The client sends the preferred language to the server. The server can use this to show the appropriate translations in for example chat messages or broadcasts.
The user of the client can then set this language in the settings once.

**EXAMPLE**:

```python
# datasrc/network.py
Messages = [
    # [..]
	NetMessageEx("Cl_Language", "language@ddnet-community.github.io", [
		NetStringStrict("m_pLanguage"),
	]),
]
```

```C++
// send on the client side
CNetMsg_Cl_Language Msg;
Msg.m_pLanguage = "en";
CMsgPacker Packer(&Msg);
Msg.Pack(&Packer);
Client()->SendMsgActive(&Packer, MSGFLAG_VITAL);
```
