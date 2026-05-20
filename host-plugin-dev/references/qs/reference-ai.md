# qs ai reference

This file is the AI-first QS reference. It compresses layout, metadata, globals, callbacks, message fields, and common helpers into one searchable document.

## Runtime profile

- Script root uses one directory per plugin
- Core files:
  - `main.java`
  - `info.prop`
  - `desc.txt`
- Optional preview assets:
  - `images/icon.png`
  - `images/preview1.png`
  - `images/preview2.png`
- Shared script/runtime rules come from `references/common/patterns-ai.md`
- QS-specific runtime constraints:
  - use JDK 9-compatible APIs only
  - prefer direct field access on documented host objects

## Minimal layout

```text
<script-name>/
├─ main.java
├─ info.prop
└─ desc.txt
```

Optional preview assets:

```text
<script-name>/
└─ images/
   ├─ icon.png
   ├─ preview1.png
   └─ preview2.png
```

## Metadata

```properties
name=脚本名称
type=1
version=1.0
author=作者名
id=脚本唯一ID
date=2025-12-1
tags=群聊辅助,娱乐功能
```

- `name`: script name
- `type`: fixed to `1`
- `version`: script version
- `author`: author name
- `id`: unique script identifier
- `date`: update date in `yyyy-M-d`
- `tags`: comma-separated labels

## Documented globals

- `myUin`: current QQ number
- `context`: QQ global `Context`
- `appPath`: script runtime relative directory
- `loader`: QQ class loader
- `pluginID`: current script ID

## Documented callbacks

- `void onMsg(Object msg)` or `void onMsg(MessageData msg)`: message callback
- `void onForbiddenEvent(String groupUin, String userUin, String opUin, long time)`: mute event
- `void onTroopEvent(String groupUin, String userUin, int type)`: group member event
- `void onClickFloatingWindow(int type, String uin)`: floating window callback
- `String getMsg(String msg, String peerUin, int chatType)`: send-text preprocessing
- `void onCreateMenu(MessageData msg)`: message long-press menu callback
- `void callbackOnRawMsg(Object msg)`: raw message callback
- `void onLoad()`: load callback
- `void onUnLoad()`: unload callback

## Menu helpers

- `String addItem(String name, String callbackName)`
- `void addTemporaryItem(String itemName, String callbackName)`
- `void removeItem(String itemID)`
- `void addMenuItem(String name, String callbackName)`

## Common callback selection

- passive automation: `onMsg(...)`
- send text preprocessing: `getMsg(String msg, String peerUin, int chatType)`
- per-message user action: `onCreateMenu(MessageData msg)` plus `addMenuItem(...)`
- chat-context utility: `onClickFloatingWindow(...)` plus `addTemporaryItem(...)`
- unload cleanup: `onUnLoad()`

## Message object

Documented `MessageData` fields include:

- `MessageContent`
- `GroupUin`
- `PeerUin`
- `UserUin`
- `MessageType`
- `IsGroup`
- `SenderNickName`
- `GroupName`
- `MessageTime`
- `mAtList`
- `IsSend`
- `FileName`
- `FileSize`
- `LocalPath`
- `FileUrl`
- `ReplyTo`
- `RecordMsg`
- `PicList`
- `PicUrlList`
- `msg`

## Send helpers

- `void sendMsg(String groupUin, String userUin, String msg)`
- `void sendPic(String groupUin, String userUin, String path)`
- `void sendSticker(String groupUin, String userUin, String path, String summary)`
- `void sendCard(String groupUin, String userUin, String card)`
- `void sendReply(String groupUin, Object msg, String text)`
- `void sendFile(String groupUin, String userUin, String path)`
- `void sendVoice(String groupUin, String userUin, String path)`
- `void sendVideo(String groupUin, String userUin, String path)`
- `void sendLike(String userUin, int count)`
- `void sendPai(String groupUin, String userUin)`
- `replyEmoji(...)`
- `forwardMsg(...)`

## Group and info helpers

- `List<MessageData> getMessageList(String groupUin, String userUin, int count)`
- `String getMemberName(String groupUin, String uin)`
- `ArrayList<GroupInfo> getGroupList()`
- `GroupInfo getGroupInfo(String groupUin)`
- `ArrayList<GroupMemberInfo> getGroupMemberList(String groupUin)`
- `GroupMemberInfo getMemberInfo(String groupUin, String uin)`
- `ArrayList<ForbiddenInfo> getForbiddenList(String groupUin)`
- `ArrayList<FriendInfo> getFriendList()`
- `boolean isFriend(String uin)`

## Storage, HTTP, and file helpers

### Config helpers

- `void putString(String configName, String key, String value)`
- `String getString(String configName, String key)`
- `String getString(String configName, String key, String def)`
- `void putInt(String configName, String key, int value)`
- `int getInt(String configName, String key, int def)`
- `void putLong(String configName, String key, long value)`
- `long getLong(String configName, String key, long def)`
- `void putBoolean(String configName, String key, boolean value)`
- `boolean getBoolean(String configName, String key, boolean def)`
- `void putFloat(String configName, String key, float value)`
- `float getFloat(String configName, String key, float def)`
- `void putDouble(String configName, String key, double value)`
- `double getDouble(String configName, String key, double def)`

### HTTP helpers

- `String httpGet(String url)`
- `String httpGet(String url, Map<String, String> headers)`
- `String httpPost(String url, Map<String, String> data)`
- `String httpPost(String url, Map<String, String> headers, Map<String, String> data)`
- `String httpPostJson(String url, String data)`
- `String httpPostJson(String url, Map<String, String> headers, String data)`
- `void httpDownload(String url, String path)`
- `void httpDownload(String url, String path, Map<String, String> headers)`

HTTP notes:

- `GET` and `POST` failures usually return empty string.
- `httpDownload(...)` path should use a script-directory relative path.
- `Map` parameters and response data are string-oriented in documented usage.

### File helpers

- `String readFileText(String path)`
- `void writeTextToFile(String path, String text)`
- `void writeTextAppendToFile(String path, String text)`
- `byte[] readFileBytes(String path)`
- `void writeBytesToFile(String path, byte[] bytes)`

### Dynamic loading

- `void load(String path)`
- `void loadJar(String jarPath)`
- `void loadDex(String path)`
- `void eval(String code)`

## Minimal patterns

### Message handling

```java
void onMsg(Object msg) {
    if (msg.IsGroup && msg.MessageContent.equals("菜单")) {
        sendMsg(msg.GroupUin, "", "已收到")
    }
}
```

### Floating item

```java
addItem("开关", "open")

public void open(String groupUin, String uin, int chatType) {
    if (chatType != 2) {
        toast("不支持私聊开启")
        return
    }
}
```

### Message menu

```java
void onCreateMenu(MessageData msg) {
    if (msg.IsGroup) {
        addMenuItem("仅群", "showGroup")
    }
}

void showGroup(MessageData msg) {
    toast("提示在" + msg.GroupUin)
}
```
