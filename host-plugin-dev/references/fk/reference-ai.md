# fk ai reference

This file is the AI-first FK reference. It compresses layout, metadata, globals, callbacks, structures, and host methods into one searchable document.

## Runtime profile

- Plugin root path:

```text
/storage/emulated/0/Android/media/com.tencent.mm/FkWeChat/Plugin/
```

- Core files:
  - `main.java`
  - `info.prop`
- Optional split module file:
  - `helper.java`
- Shared script/runtime rules come from `references/common/patterns-ai.md`
- FK-specific runtime constraints:
  - `helper.java` is a normal optional split module loaded through `loadJava(...)`
  - low-level CGI and CDN callbacks are part of the documented host surface

## Minimal layout

```text
<plugin-name>/
├─ info.prop
├─ main.java
└─ helper.java
```

- `helper.java` is optional and is typically loaded through `loadJava(...)`

## Metadata

```properties
name=test
author=雲上升
version=1.0.0
desc=测试
```

- `name`: plugin name
- `author`: plugin author
- `version`: plugin version
- `desc`: plugin description

## Documented globals

- `pluginName`: plugin name
- `pluginPath`: plugin path
- `pluginAuthor`: plugin author
- `pluginVersion`: plugin version
- `hostVerCode`: host version code
- `hostVerName`: host version name
- `hostLoader`: host class loader
- `hostContext`: host Android `Context`
- `myWxId`: current account wxid

## Documented callbacks

- `onLoad()`: plugin initialization callback
- `onUnload()`: plugin unload callback
- `onMsg(msg)`: message callback
- `onMsgMenu(msg)`: message menu callback
- `onCgiRequ(data)`: CGI request callback
- `onCgiResp(data)`: CGI response callback
- `onCdnDownload(info)`: CDN download callback
- `onCdnUpload(info)`: CDN upload callback

## Menu extension

- `void addMenuItem(String title, String path, Object action)`

Parameters:

- `title`: menu label
- `path`: icon resource path, empty string for default icon
- `action`: callback to run when clicked

## Message structure

### Base fields

- `type`: message type
- `content`: message text content
- `talker`: target session id
- `isSend`: message direction flag
- `msgId`: local message id
- `createTime`: timestamp in milliseconds
- `rawContent`: raw content
- `sendTalker`: sender wxid
- `imgPath`: image local path
- `msgSvrId`: server message id
- `status`: message status
- `lvbuffer`: extra bytes

### Extended fields

- `atList`: mentioned wxid list
- `isAtMe`: whether current account was mentioned
- `isAtAll`: whether all-members mention was used
- `isAnnounce`: whether message is a group announcement

### Helper methods

- `isGroupChat()`
- `isChatRoom()`
- `isPrivateChat()`
- `isSend()`
- `isText()`
- `isImage()`
- `isPic()`
- `isVoice()`
- `isVideo()`
- `isEmoji()`
- `isGif()`
- `isLocation()`
- `isFile()`
- `isCard()`
- `isSystem()`
- `isRevoke()`
- `isTransfer()`
- `isRedPacket()`
- `isReply()`
- `isQuote()`
- `isNotify()`
- `isAtMe()`
- `isAtAll()`
- `isAnnounce()`

## CGI structure

- `type`: CGI type
- `uri`: request URI
- `json`: JSON payload string

## CDN structures

### Cdn download common fields

- `methodName`
- `fileKey`
- `url`
- `host`
- `savePath`
- `aeskey`
- `fileid`
- `fileType`
- `bizid`
- `apptype`
- `chatType`
- `msgType`
- `expectFileSize`
- `taskStartTime`
- `isSilentTask`
- `isStorageMode`
- `isSmallVideo`
- `isAutoStart`
- `isHLSVideo`
- `allow_mobile_net_download`
- `is_resume_task`
- `supportFormats`

### Cdn upload common fields

- `methodName`
- `fileKey`
- `toUser`
- `host`
- `filemd5`
- `filePath`
- `thumbfilePath`
- `uri`
- `chatType`
- `fileType`
- `fileFormat`
- `bizid`
- `apptype`
- `sendmsgFromCDN`
- `checkExistOnly`
- `isSmallVideo`
- `isStorageMode`
- `trySafeCdn`
- `bizSnsPreUpload`

## Send helpers

- `void sendText(String wxid, String text)`
- `void sendImage(String talker, String path)`
- `void sendImage(String talker, String path, boolean isRaw)`
- `void sendNetScene(Object netScene)`
- `void sendFile(String wxid, String path)`
- `void sendEmoji(String wxid, String md5)`
- `void sendCard(String wxid, String xml)`
- `void sendXml(String wxid, String xml, int type)`
- `void sendMusic(String wxid, String title, String desc, String musicUrl, String thumbPath, String lyric)`
- `void sendMedia(String wxid, Object mediaObj, String appId)`

## Load helpers

- `void loadDex(String path)`
- `void loadJar(String path)`
- `void loadJava(String path)`
- `void loadAar(String path)`

## Group management helpers

- `void deleteGroupMember(String talker, String wxid)`
- `void deleteGroupMember(String talker, List<String> list)`
- `void addGroupMember(String talker, String wxid)`
- `void addGroupMember(String talker, List<String> list)`
- `void inviteGroupMember(String talker, String wxid)`
- `void inviteGroupMember(String talker, List<String> list)`
- `void createGroup(String topic, List<String> list)`
- `void dismissGroup(String talker)`

## Data query helpers

- `List<Map<String, Object>> executeQuery(String sql)`
- `List<Map<String, Object>> getFriendList()`
- `List<Map<String, Object>> getGroupList()`
- `List<String> getGroupMemberList(String talker)`
- `String getAvatarUrl(String talker)`
- `String getUserRemark(String talker)`
- `String getUserName(String talker)`
- `String getUserName(String groupId, String talker)`
- `String getGroupNotice(String talker)`
- `Map<String, Object> getMsg(String talker, long msgId)`
- `Map<String, Object> getEmojiInfo(String md5)`
- `String getEmojiUrl(String md5)`
- `long getMsgCount(String talker)`
- `List<Map<String, Object>> getMsgs(String talker, long startTime)`
- `List<Map<String, Object>> getContact(String keyword)`

## Storage helpers

- `void setBoolean(String key, boolean value)`
- `boolean getBoolean(String key, boolean def)`
- `void setString(String key, String value)`
- `String getString(String key, String def)`
- `void setInt(String key, int value)`
- `int getInt(String key, int def)`
- `void setFloat(String key, float value)`
- `float getFloat(String key, float def)`
- `void setLong(String key, long value)`
- `long getLong(String key, long def)`
- `boolean contains(String key)`
- `void remove(String key)`
- `void clear()`

## Media path helpers

- `String getAvatarPath(String talker)`
- `String getVideoPath(String md5)`
- `String getVoicePath(String md5)`
- `String getImagePath(String md5)`
- `String getMediaPath(Object msg)`
- `String getMediaPath(int type, String md5)`

## File helpers

- `boolean deleteFile(File file)`
- `void copyDirectory(File srcDir, File destDir)`
- `boolean createFile(String filePath)`
- `void copyFile(String srcPath, String destPath)`
- `void copyFile(File src, File dest)`

## Audio helpers

- `int getAudioType(String path)`
- `int wavToSilk(String wavPath, String silkPath)`
- `int wavToSilk(String wavPath, String silkPath, int sampleRate)`
- `int audioToSilk(String audioPath, String silkPath)`
- `int silkToMp3(String silkPath, String mp3Path)`
- `int audioToPcm(String audioPath, String pcmPath)`

## Other helpers

- `void toast(String text)`
- `ClassLoader getAppLoader(String pkg)`
- `void log(Object text)`
- `void print(Object... args)`
- `String getMyWxId()`
- `Map<String, Object> getMyUserInfo()`
- `Activity getTopActivity()`

## Minimal patterns

### Message handling

```java
onMsg(msg) {
    var content = msg.content
    var talker = msg.talker
    if (content == null) return

    if (content == "早上好") {
        sendText(talker, "早上好！")
    }
}
```

### Menu action

```java
onMsgMenu(msg) {
    addMenuItem("语音转文字", "", () -> {
        sendText(msg.talker, "处理中")
    })
}
```

### Dynamic loading

```java
onLoad() {
    loadJava("helper")
}
```

### CGI observation

```java
onCgiResp(data) {
    log("uri=" + data.uri)
    log("type=" + data.type)
    log("json=" + data.json)
}
```

### CDN observation

```java
onCdnDownload(data) {
    log("url=" + data.url)
    log("savePath=" + data.savePath)
    log("fileType=" + data.fileType)
}
```
