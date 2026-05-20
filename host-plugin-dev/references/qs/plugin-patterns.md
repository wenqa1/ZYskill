# qs plugin patterns

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
name=脚本名称
type=1
version=1.0
author=作者名
id=脚本唯一ID
date=2025-12-1
tags=群聊辅助,娱乐功能
```

`main.java`

```java
void onMsg(Object msg) {
    if (msg.IsGroup && msg.MessageContent.equals("菜单")) {
        sendMsg(msg.GroupUin, "", "已收到")
    }
}
```

## Metadata

- `name`: script name
- `type`: script type
- `version`: script version
- `author`: author name
- `id`: unique script identifier
- `date`: update date
- `tags`: comma-separated labels

## Style rules

- Apply the shared rules in `references/common/patterns-ai.md` first.
- Keep root-scope callbacks and helper functions.
- Do not add explicit imports for packages already covered by the shared default-import rules unless this workspace proves QS is different.
- Direct field access on message and entity objects is preferred.
- Use JDK 9-compatible APIs only.

## Common message fields

The documented `MessageData` fields include:

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

## Common send helpers

Documented send helpers include:

- `sendMsg(String groupUin, String userUin, String msg)`
- `sendPic(String groupUin, String userUin, String path)`
- `sendSticker(String groupUin, String userUin, String path, String summary)`
- `sendCard(String groupUin, String userUin, String card)`
- `sendReply(String groupUin, Object msg, String text)`
- `sendFile(String groupUin, String userUin, String path)`
- `sendVoice(String groupUin, String userUin, String path)`
- `sendVideo(String groupUin, String userUin, String path)`
- `sendLike(String userUin, int count)`
- `sendPai(String groupUin, String userUin)`
- `replyEmoji(...)`
- `forwardMsg(...)`

## Group and info helpers

Examples:

- `getMessageList(...)`
- `getMemberName(...)`
- `getGroupList()`
- `getGroupInfo(String groupUin)`
- `getGroupMemberList(String groupUin)`
- `getMemberInfo(String groupUin, String uin)`
- `getForbiddenList(String groupUin)`
- `getFriendList()`
- `isFriend(String uin)`

## Storage, HTTP, and file helpers

- Config helpers: `putString`, `getString`, `putInt`, `getInt`, `putLong`, `getLong`, `putBoolean`, `getBoolean`, `putFloat`, `getFloat`, `putDouble`, `getDouble`
- HTTP helpers: `httpGet`, `httpPost`, `httpPostJson`, `httpDownload`
- File helpers: `readFileText`, `writeTextToFile`, `writeTextAppendToFile`, `readFileBytes`, `writeBytesToFile`
- Dynamic loading: `load(String path)`, `loadJar(String jarPath)`, `loadDex(String path)`, `eval(String code)`

## Menu patterns

Floating menu:

```java
addItem("开关", "open")

public void open(String groupUin, String uin, int chatType) {
    if (chatType != 2) {
        toast("不支持私聊开启")
        return
    }
}
```

Long-press menu:

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

## Verification guidance

- Use the documented `name/type/version/author/id/date/tags` metadata layout.
- Keep callback spelling aligned with QS docs, especially `onUnLoad()`.
- Use the documented helper names and parameter order.
- Use `appPath` for script-relative file loading and downloads.
