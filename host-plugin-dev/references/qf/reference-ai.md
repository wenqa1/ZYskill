# qf ai reference

This file is the AI-first QF reference. It compresses layout, metadata, globals, callbacks, message fields, and common helpers into one searchable document.

## Runtime profile

- Script root uses one directory per plugin
- Core files:
  - `main.java`
  - `info.prop`
  - `desc.txt`
- Shared script/runtime rules come from `references/common/patterns-ai.md`
- QF-specific runtime constraints:
  - Java 8+ syntax is supported, including lambda
  - direct field access on documented host objects is preferred

## Minimal layout

```text
<script-name>/
├─ main.java
├─ info.prop
└─ desc.txt
```

## Metadata

```properties
id=脚本唯一标识符
pluginName=脚本名称
author=作者
versionCode=1
```

- `id`: unique script identifier
- `pluginName`: script display name
- `author`: author name
- `versionCode`: numeric version

## Documented globals

- `context`: host app global `Context`
- `pluginId`: current script ID
- `classLoader`: QQ host class loader
- `pluginPath`: current script folder absolute path
- `myUin`: current logged-in QQ number

## Documented callbacks

- `void onMsg(Object msgData)`: message callback
- `void joinGroup(String groupUin, String userUin)`: group join event
- `void quitGroup(String groupUin, String userUin)`: group quit event
- `void shutUpGroup(String groupUin, String userUin, long time, String opUin)`: mute event
- `void chatInterface(int chatType, String peerUin, String name)`: chat interface entry
- `void onPaiYiPai(String peerUin, int chatType, String opUin)`: pai event
- `String getMsg(String raw)`: send-text preprocessing
- `void unLoadPlugin()`: unload callback

## Menu helpers

- `void addItem(String menuName, String callbackName)`
- `void addMenuItem(String menuName, String callbackName, int[] messageTypes)`

Supported callback signatures:

```java
void callback(int chatType, String peerUin, String name)
void callback(int chatType, String peerUin, String name, Object contact)
void callback(Object msgData)
```

## Common callback selection

- passive message automation: `onMsg(Object msgData)`
- send text preprocessing: `String getMsg(String raw)`
- current-chat context logic: `chatInterface(int chatType, String peerUin, String name)`
- unload cleanup: `unLoadPlugin()`
- message menu actions: `addMenuItem(...)` plus `void callback(Object msgData)`

## Message object

Documented `MsgData` fields include:

- `type`
- `msgType`
- `peerUin`
- `peerUid`
- `userUin`
- `userUid`
- `time`
- `msgId`
- `msg`
- `path`
- `atList`
- `atMap`
- `data`
- `contact`

## Send helpers

- `void sendMsg(String peerUin, String content, int chatType)`
- `void sendPic(String peerUin, String path, int chatType)`
- `void sendPtt(String peerUin, String path, int chatType)`
- `void sendCard(String peerUin, String json, int chatType)`
- `void sendFile(String peerUin, String path, int chatType)`
- `void sendVideo(String peerUin, String path, int chatType)`
- `void sendReplyMsg(String peerUin, long msgId, String content, int chatType)`
- `void sendPai(String userUin, String peerUin, int chatType)`

Many send helpers also have object-overload variants in the runtime.

## Group and friend helpers

- `ArrayList<?> getAllFriend()`
- `boolean isFriend(String uin)`
- `void sendZan(String uin, int count)`
- `ArrayList<?> getGroupList()`
- `ArrayList<?> getGroupMemberList(String groupUin)`
- `Object getGroupInfo(String groupUin)`
- `Object getMemberInfo(String groupUin, String userUin)`
- `void shutUp(String groupUin, String userUin, long seconds)`
- `void shutUpAll(String groupUin, boolean enabled)`
- `void kickGroup(String groupUin, String userUin, boolean blacklist)`

## Storage and loading

- Config path pattern: `plugin/脚本ID/config/`

### Config helpers

- `void putString(String key, String value)`
- `String getString(String key, String defValue)`
- `void putInt(String key, int value)`
- `int getInt(String key, int defValue)`
- `void putLong(String key, long value)`
- `long getLong(String key, long defValue)`
- `void putBoolean(String key, boolean value)`
- `boolean getBoolean(String key, boolean defValue)`

### Dynamic loading helpers

- `void loadJava(String path)`
- `void loadJar(String path)`
- `void loadDex(String path)`
- `void registerActivity(Class<? extends Activity> clazz)`

## Minimal patterns

### Message handling

```java
void onMsg(Object msgData) {
    if (msgData.msg != null && msgData.msg.equals("菜单")) {
        sendMsg(msgData.peerUin, "已收到", msgData.type)
    }
}
```

### Floating menu

```java
addItem("开关", "toggle")

void toggle(int chatType, String peerUin, String name) {
    toast("clicked: " + peerUin)
}
```

### Message menu

```java
addMenuItem("处理消息", "handleMsg", null)

void handleMsg(Object msgData) {
    log("处理: " + msgData.msg)
}
```
