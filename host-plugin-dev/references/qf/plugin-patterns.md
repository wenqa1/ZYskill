# qf plugin patterns

## Minimal files

Default standalone layout:

```text
<script-name>/
├─ info.prop
├─ desc.txt
└─ main.java
```

`info.prop`

```properties
id=脚本唯一标识符
pluginName=脚本名称
author=作者
versionCode=1
```

`main.java`

```java
void onMsg(Object msgData) {
    if (msgData.msg != null && msgData.msg.equals("菜单")) {
        sendMsg(msgData.peerUin, "已收到", msgData.type)
    }
}
```

## Metadata

- `id`: unique script identifier
- `pluginName`: script display name
- `author`: author name
- `versionCode`: numeric script version

## Style rules

- Apply the shared rules in `references/common/patterns-ai.md` first.
- Keep root-scope callbacks and helper functions.
- Java 8+ syntax is supported, including lambda.
- `context`, `classLoader`, and host APIs are globally available.
- Direct field access on host objects is preferred over getters.

## Core message object

The documented `MsgData` fields include:

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

## Common send helpers

Documented send helpers include:

- `sendMsg(String peerUin, String content, int chatType)`
- `sendPic(String peerUin, String path, int chatType)`
- `sendPtt(String peerUin, String path, int chatType)`
- `sendCard(String peerUin, String json, int chatType)`
- `sendFile(String peerUin, String path, int chatType)`
- `sendVideo(String peerUin, String path, int chatType)`
- `sendReplyMsg(String peerUin, long msgId, String content, int chatType)`
- `sendPai(String userUin, String peerUin, int chatType)`

Object-overload variants are also documented for many send methods.

## Group and friend helpers

Examples:

- `getAllFriend()`
- `isFriend(String uin)`
- `sendZan(String uin, int count)`
- `getGroupList()`
- `getGroupMemberList(String groupUin)`
- `getGroupInfo(String groupUin)`
- `getMemberInfo(String groupUin, String userUin)`
- `shutUp(String groupUin, String userUin, long seconds)`
- `shutUpAll(String groupUin, boolean enabled)`
- `kickGroup(String groupUin, String userUin, boolean blacklist)`

## Storage and loading

- Config values are stored under `plugin/脚本ID/config/` JSON files.
- Use `putString`, `putInt`, `putLong`, `putBoolean` and matching getters.
- Dynamic loading helpers:
  - `loadJava(String path)`
  - `loadJar(String path)`
  - `loadDex(String path)`
  - `registerActivity(Class<? extends Activity> clazz)`

## Menu patterns

Floating menu:

```java
addItem("开关", "toggle")

void toggle(int chatType, String peerUin, String name) {
    toast("clicked: " + peerUin)
}
```

Message long-press menu:

```java
addMenuItem("处理消息", "handleMsg", null)

void handleMsg(Object msgData) {
    log("处理: " + msgData.msg)
}
```

## Verification guidance

- Use the documented `pluginName/versionCode` metadata layout.
- Use `unLoadPlugin()` for unload handling.
- Prefer the documented overload signatures instead of guessing parameter order.
- Verify whether the local runtime expects `peerUin` or a `contact` object before choosing an overload.
