# qf callbacks

## Documented callbacks

Documented event callbacks:

- `void onMsg(Object msgData)`: receive a `MsgData` message object
- `void joinGroup(String groupUin, String userUin)`: member joined group
- `void quitGroup(String groupUin, String userUin)`: member left group
- `void shutUpGroup(String groupUin, String userUin, long time, String opUin)`: mute event
- `void chatInterface(int chatType, String peerUin, String name)`: entered chat interface
- `void onPaiYiPai(String peerUin, int chatType, String opUin)`: pai event
- `String getMsg(String raw)`: send-text preprocessing
- `void unLoadPlugin()`: plugin stop or unload

## Menu callbacks

Floating menu:

- `addItem(String menuName, String callbackName)`

Supported callback signatures:

```java
void callback(int chatType, String peerUin, String name)
void callback(int chatType, String peerUin, String name, Object contact)
```

Message long-press menu:

- `addMenuItem(String menuName, String callbackName, int[] messageTypes)`

Callback signature:

```java
void callback(Object msgData)
```

## Guidance

- Use `onMsg(...)` for passive message automation.
- Use `getMsg(String raw)` only for text preprocessing before send.
- Use `chatInterface(...)` when behavior should bind to the current chat session.
- Use `unLoadPlugin()` for cleanup.
- Match callback parameter order exactly as documented before writing menu or group-event logic.
