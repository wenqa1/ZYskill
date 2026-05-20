# wa ai reference

This file is the AI-first WA reference. It compresses layout, metadata, globals, callbacks, structures, and common methods into one searchable document.

## Runtime profile

- Script entry file: `main.java`
- Metadata file: `info.prop`
- Optional local config file: `config.prop`
- Shared script/runtime rules come from `references/common/patterns-ai.md`
- WA-specific runtime constraints:
  - network and download helpers are asynchronous
  - prefer high-level WA helpers before reflection or hooks
  - `getTargetTalker()` is current-chat oriented and should not replace inbound message targets blindly

## Minimal layout

```text
<plugin-name>/
├─ info.prop
├─ main.java
├─ README.md
└─ config.prop
```

## Metadata

```properties
name = [插件名称]
author = [作者]
version = 1.0.0
updateTime = 20260516
```

- `name`: plugin display name
- `author`: plugin author
- `version`: plugin version
- `updateTime`: update date in `YYYYMMDD`

## Documented globals

- `hostContext`: host Android `Context`
- `hostVerName`: host version name
- `hostVerCode`: host version code
- `hostVerClient`: host client identifier
- `moduleVer`: module version
- `cacheDir`: cache directory for temp files
- `pluginDir`: plugin-owned directory
- `pluginId`: plugin identifier
- `pluginName`: plugin name
- `pluginAuthor`: plugin author
- `pluginVersion`: plugin version
- `pluginUpdateTime`: plugin update time

## Documented callbacks

- `void onLoad()`: plugin load hook
- `void onUnload()`: plugin unload hook
- `void openSettings()`: settings entry
- `void onHandleMsg(Object msgInfoBean)`: inbound message callback
- `boolean onClickSendBtn(String text)`: send-button interception callback
- `void onMemberChange(String type, String groupWxid, String userWxid, String userName)`: group member event
- `void onNewFriend(String wxid, String ticket, int scene)`: new-friend request callback
- `void onRecvPayMsg(Object payMsgBean)`: payment callback

## Common callback selection

- passive listening or auto reply: `onHandleMsg(Object msgInfoBean)`
- user-triggered command on input box text: `boolean onClickSendBtn(String text)`
- one-time setup or hook registration: `onLoad()`
- cleanup or unhooking: `onUnload()`
- plugin menu/settings UI: `openSettings()`
- group join or leave reaction: `onMemberChange(...)`

## Message structure

### `MsgInfoBean` methods

- `long getMsgId()`
- `int getType()`
- `long getCreateTime()`
- `String getTalker()`
- `String getSendTalker()`
- `String getContent()`
- `String getMsgSource()`
- `List<String> getAtUserList()`
- `boolean isAnnounceAll()`
- `boolean isNotifyAll()`
- `boolean isAtMe()`
- `ImageMsg getImageMsg()`
- `QuoteMsg getQuoteMsg()`
- `PatMsg getPatMsg()`
- `FileMsg getFileMsg()`
- `boolean isPrivateChat()`
- `boolean isOpenIM()`
- `boolean isGroupChat()`
- `boolean isChatroom()`
- `boolean isImChatroom()`
- `boolean isOfficialAccount()`
- `boolean isSend()`
- `boolean isText()`
- `boolean isImage()`
- `boolean isVoice()`
- `boolean isShareCard()`
- `boolean isVideo()`
- `boolean isEmoji()`
- `boolean isLocation()`
- `boolean isApp()`
- `boolean isVoip()`
- `boolean isVoipVoice()`
- `boolean isVoipVideo()`
- `boolean isSystem()`
- `boolean isRecalled()`
- `boolean isLink()`
- `boolean isTransfer()`
- `boolean isRedBag()`
- `boolean isVideoNumberVideo()`
- `boolean isNote()`
- `boolean isQuote()`
- `boolean isPat()`
- `boolean isFile()`

### `ImageMsg`

- `String getMd5()`
- `String getBigImgUrl()`
- `String getMidImgUrl()`
- `String getThumbUrl()`
- `String getKey()`

### `QuoteMsg`

- `String getTitle()`
- `String getMsgSource()`
- `String getSendTalker()`
- `String getDisplayName()`
- `String getTalker()`
- `int getType()`
- `String getContent()`

### `PatMsg`

- `String getTalker()`
- `String getFromUser()`
- `String getPattedUser()`
- `String getTemplate()`
- `long getCreateTime()`

### `FileMsg`

- `String getTitle()`
- `long getSize()`
- `String getExt()`
- `String getMd5()`
- `String getUrl()`
- `String getKey()`

### `PayMsgBean`

- `int getTimestamp()`
- `String getUsername()`
- `String getDisplayName()`
- `double getFee()`
- `int getStatus()`
- `String getStatusDesc()`

## Contact and chat helpers

- `String getLoginWxid()`
- `String getLoginAlias()`
- `String getTargetTalker()`
- `List<FriendInfo> getOfficialList()`
- `List<FriendInfo> getFriendList()`
- `String getFriendNickName(String friendWxid)`
- `String getFriendRemarkName(String friendWxid)`
- `String getFriendDisplayName(String friendWxid, String roomId)`
- `String getFriendName(String friendWxid)`
- `String getFriendName(String friendWxid, String roomId)`
- `String getAvatarUrl(String username)`
- `String getAvatarUrl(String username, boolean isBigHeadImg)`
- `List<GroupInfo> getGroupList()`
- `List<String> getGroupMemberList(String groupWxid)`
- `int getGroupMemberCount(String groupWxid)`
- `void addChatroomMember(String chatroomId, String addMember)`
- `void addChatroomMember(String chatroomId, List<String> addMemberList)`
- `void inviteChatroomMember(String chatroomId, String inviteMember)`
- `void inviteChatroomMember(String chatroomId, List<String> inviteMemberList)`
- `void delChatroomMember(String chatroomId, String delMember)`
- `void delChatroomMember(String chatroomId, List<String> delMemberList)`
- `void verifyUser(String wxid, String ticket, int scene)`
- `void verifyUser(String wxid, String ticket, int scene, int privacy)`

Notes:

- `talker` is the target conversation id.
- private chat usually uses friend `wxid`.
- group chat usually uses `xxx@chatroom`.
- `getTargetTalker()` is for the currently open chat, not for arbitrary inbound messages.

## Message send helpers

- `void sendText(String talker, String content)`
- `void sendText(String talker, String content, Consumer<Long> callback)`
- `void sendVoice(String talker, String sendPath)`
- `void sendVoice(String talker, String sendPath, int duration)`
- `void sendImage(String talker, String sendPath)`
- `void sendImage(String talker, String sendPath, String appId)`
- `void sendVideo(String talker, String sendPath)`
- `void sendEmoji(String talker, String sendPath)`
- `void sendPat(String talker, String pattedUser)`
- `void sendShareCard(String talker, String wxid)`
- `void sendLocation(String talker, String poiName, String label, String x, String y, String scale)`
- `void sendLocation(String talker, JSONObject jsonObj)`
- `void sendCipherMsg(String talker, String title, String content)`
- `void sendAppBrandMsg(String talker, String title, String pagePath, String ghName)`
- `void sendNoteMsg(String talker, String content)`
- `void sendQuoteMsg(String talker, long msgId, String content)`
- `void revokeMsg(long msgId)`
- `long insertSystemMsg(String talker, String content, long createTime)`
- `List<MsgInfoBean> queryHistoryMsg(String talker, long startTime, int count)`
- `void downloadImg(String md5, String cdnUrl, String aesKey, String savePath)`
- `void downloadImg(MsgInfoBean.ImageMsg imageMsg, String savePath)`

## Media and share helpers

- `void sendMediaMsg(String talker, Object mediaMessage, String appId)`
- `void shareFile(String talker, String title, String filePath, String appId)`
- `void shareMiniProgram(String talker, String title, String description, String userName, String path, byte[] thumbData, String appId)`
- `void shareMusic(String talker, String title, String description, String musicUrl, String musicDataUrl, byte[] thumbData, String appId)`
- `void shareMusicVideo(String talker, String title, String description, String musicUrl, String musicDataUrl, String singerName, int duration, String songLyric, byte[] thumbData, String appId)`
- `void shareText(String talker, String text, String appId)`
- `void shareVideo(String talker, String title, String description, String videoUrl, byte[] thumbData, String appId)`
- `void shareWebpage(String talker, String title, String description, String webpageUrl, byte[] thumbData, String appId)`

## Network helpers

- `void get(String url, Map<String, String> headerMap, Consumer<String> callback)`
- `void get(String url, Map<String, String> headerMap, long timeout, Consumer<String> callback)`
- `void post(String url, Map<String, String> paramMap, Map<String, String> headerMap, Consumer<String> callback)`
- `void post(String url, Map<String, String> paramMap, Map<String, String> headerMap, long timeout, Consumer<String> callback)`
- `void download(String url, String path, Map<String, String> headerMap, Consumer<File> callback)`
- `void download(String url, String path, Map<String, String> headerMap, long timeout, Consumer<File> callback)`

Rules:

- all network helpers are async
- callback result may be `null`
- use `cacheDir` for temp download output
- use `JSONObject` or `JSONArray` for JSON parsing
- do not treat `get(...)`, `post(...)`, or `download(...)` as synchronous return-value APIs

## Config helpers

- `String getString(String key, String defValue)`
- `Set<String> getStringSet(String key, Set<String> defValue)`
- `boolean getBoolean(String key, boolean defValue)`
- `int getInt(String key, int defValue)`
- `float getFloat(String key, float defValue)`
- `long getLong(String key, long defValue)`
- `void putString(String key, String value)`
- `void putStringSet(String key, Set<String> value)`
- `void putBoolean(String key, boolean value)`
- `void putInt(String key, int value)`
- `void putFloat(String key, float value)`
- `void putLong(String key, long value)`

Notes:

- these helpers operate on the plugin config store
- prefer them over manual `config.prop` parsing for normal plugin settings

## Audio helpers

- `int mp3ToSilk(String mp3Path, String silkPath)`
- `int mp3ToSilk(String mp3Path, String silkPath, int hz)`
- `int silkToMp3(String silkPath, String mp3Path)`
- `int silkToMp3(String silkPath, String mp3Path, int hz)`
- `long getDuration(String filePath)`

## Dynamic loading and misc helpers

- `void eval(String code)`
- `void loadJava(String path)`
- `void loadDex(String path)`
- `void loadJar(String path)`
- `void log(Object msg)`
- `void toast(String text)`
- `void delay(long millis, Runnable action)`
- `void notify(String title, String text)`
- `Activity getTopActivity()`
- `void uploadDeviceStep(long step)`

Notes:

- relative paths in `loadJava`, `loadDex`, and `loadJar` are resolved from the plugin directory in documented usage
- `toast(...)` is for lightweight user feedback
- `log(...)` is preferred for diagnostics

## Hook helpers

- `HookHandle hookBefore(Member member, Consumer<XC_MethodHook.MethodHookParam> callback)`
- `HookHandle hookAfter(Member member, Consumer<XC_MethodHook.MethodHookParam> callback)`
- `HookHandle hookReplace(Member member, Function<XC_MethodHook.MethodHookParam, Object> callback)`
- `void unhook(HookHandle handle)`

## DexKit helpers

- `List<Class<?>> findClassList(List<String> usingStrings)`
- `List<Member> findMemberList(List<String> usingStrings)`

## Reflection helpers

- `Method firstMethod(Object instance, String methodName)`
- `Method firstMethod(Object instance, String methodName, int paramCount)`
- `Constructor firstConstructor(Object instance, int paramCount)`
- `Field firstField(Object instance, String fieldName)`
- `Object invokeMethod(Object instance, String methodName)`
- `Object invokeMethod(Object instance, String methodName, Object[] params)`
- `Object invokeMethod(Object instance, String methodName, int paramCount)`
- `Object invokeMethod(Object instance, String methodName, int paramCount, Object[] params)`
- `Object createInstance(Object instance, int paramCount)`
- `Object createInstance(Object instance, int paramCount, Object[] params)`
- `Object getField(Object instance, String fieldName)`
- `void setField(Object instance, String fieldName, Object value)`

## Timeline helpers

- `void uploadText(String content)`
- `void uploadText(String content, String sdkId, String sdkAppName)`
- `void uploadText(JSONObject jsonObj)`
- `void uploadTextAndPicList(String content, String picPath)`
- `void uploadTextAndPicList(String content, String picPath, String sdkId, String sdkAppName)`
- `void uploadTextAndPicList(String content, List<String> picPathList)`
- `void uploadTextAndPicList(String content, List<String> picPathList, String sdkId, String sdkAppName)`
- `void uploadTextAndPicList(JSONObject jsonObj)`

## Minimal patterns

### Passive message handling

```beanshell
void onHandleMsg(Object msgInfoBean) {
    if (!msgInfoBean.isSend()) return
    if (!msgInfoBean.isText()) return

    var talker = msgInfoBean.getTalker()
    var content = msgInfoBean.getContent()
    if (content == null) return
}
```

### Send-button command pattern

```beanshell
boolean onClickSendBtn(String text) {
    var input = text == null ? "" : text.trim()
    if (input.length() == 0) return false

    var talker = getTargetTalker()
    sendText(talker, "正在处理...")
    return true
}
```

### Async HTTP pattern

```beanshell
get(url, null, body -> {
    if (body == null) return

    var json = new JSONObject(body)
    sendText(getTargetTalker(), json.optString("msg"))
})
```

### Hook lifecycle pattern

```beanshell
var hookRef = null

void onLoad() {
    var method = com.tencent.mm.ui.MoreTabUI.class.getDeclaredMethod("onResume")
    hookRef = hookBefore(method, param -> {
        log("onResume before")
    })
}

void onUnload() {
    if (hookRef != null) {
        unhook(hookRef)
        hookRef = null
    }
}
```
