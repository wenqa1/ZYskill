# wa plugin patterns

## Minimal files

Default standalone layout:

```text
<plugin-name>/
├─ info.prop
├─ main.java
├─ README.md
└─ config.prop
```

`info.prop`

```properties
name = [插件名称]
author = [作者]
version = 1.0.0
updateTime = 20260516
```

`main.java`

```beanshell
boolean onClickSendBtn(String text) {
    var talker = getTargetTalker()
    var input = text == null ? "" : text.trim()
    if (input.length() == 0) return false

    sendText(talker, "正在处理...")
    return true
}

void onLoad() {
    log("plugin loaded: ${pluginName}")
}
```

`README.md`

```md
# 插件名称

这是一个给新手使用的小插件。

## 这是做什么的

它会帮你处理指定内容，并把结果直接发到当前聊天里。

## 怎么触发

先在输入框输入指定内容，然后点击发送按钮。

## 成功后会看到什么

处理成功后，聊天里会收到对应结果。

## 使用说明

按提示使用即可，不需要额外设置。
```

## README template for beginners

Default sections:

- `## 这是做什么的`
- `## 怎么触发`
- `## 成功后会看到什么`
- `## 使用说明`

Keep the wording simple. Do not explain APIs, JSON, callbacks, hooks, or code structure unless the user explicitly asks for technical documentation.

## Style rules for this runtime

- Apply the shared rules in `references/common/patterns-ai.md` first.
- Keep WA callbacks and helper functions at file root.
- Default to processing only self-sent messages unless the user explicitly asks for handling other people's messages.
- Do not invent APIs. Use only callbacks, globals, and helper methods that are documented in WA docs or already present in reference plugins.

## API truth sources

Before using less-common features, verify them against:

- `references/wa/reference-ai.md`
- existing same-host plugins in the workspace

If a method is not listed there, look for the same method in existing plugins before using it. If it still cannot be verified, do not use it.

## Common globals

- `cacheDir`: temporary output, downloads, generated media
- `pluginDir`: plugin-owned files and local configuration
- `pluginName`, `pluginVersion`: logging and diagnostics
- `hostVerName`, `hostVerCode`, `hostVerClient`: compatibility branching

## Common callbacks

- `void onLoad()`
- `void onUnload()`
- `void openSettings()`
- `void onHandleMsg(Object msgInfoBean)`
- `boolean onClickSendBtn(String text)`
- `void onMemberChange(String type, String groupWxid, String userWxid, String userName)`
- `void onNewFriend(String wxid, String ticket, int scene)`
- `void onRecvPayMsg(Object payMsgBean)`

## Preferred query pattern

For user-initiated query, search, generation, and tool-style plugins, prefer:

```beanshell
boolean onClickSendBtn(String text) {
    var input = text == null ? "" : text.trim()
    if (input.length() == 0) return false

    var talker = getTargetTalker()
    // Match the command or link here.
    // Return false when the input does not belong to this plugin.

    sendText(talker, "正在处理...")
    return true
}
```

Use `onHandleMsg(...)` mainly for passive listening or auto-reply behavior when the user explicitly wants that mode.

## Passive message handling pattern

Typical guard order:

```beanshell
void onHandleMsg(Object msgInfoBean) {
    if (!msgInfoBean.isSend()) return
    if (!msgInfoBean.isText()) return

    var talker = msgInfoBean.getTalker()
    var sender = msgInfoBean.getSendTalker()
    var content = msgInfoBean.getContent()
}
```

Useful checks:

- `isText()`
- `isImage()`
- `isAtMe()`
- `isGroupChat()`
- `isPrivateChat()`
- `isQuote()`
- `isPat()`
- `isFile()`

Useful getters:

- `getTalker()`
- `getSendTalker()`
- `getContent()`
- `getMsgId()`
- `getImageMsg()`
- `getQuoteMsg()`
- `getPatMsg()`
- `getFileMsg()`

## Sending and replying

- reply text: `sendText(talker, content)`
- mention in group: `sendText(talker, "[AtWx=${wxid}] 你好")`
- current chat target: `getTargetTalker()`
- send image from local file: `sendImage(talker, "${cacheDir}/demo.jpg")`
- quote reply: `sendQuoteMsg(talker, msgId, "reply")`

## HTTP and download

All HTTP helpers are asynchronous.

```beanshell
get(url, null, body -> {
    if (body == null) return
    log(body)
})

download(imgUrl, "${cacheDir}/demo.jpg", null, file -> {
    if (file != null) {
        sendImage(getTargetTalker(), file.getAbsolutePath())
    }
})
```

Rules:

- never treat `get` or `post` as returning response text directly
- check callback parameters for `null`
- prefer `cacheDir` for temporary media files
- if the response is JSON, prefer `org.json.JSONObject` and `org.json.JSONArray` for parsing

Example:

```beanshell
get(url, null, body -> {
    if (body == null) return

    var json = new JSONObject(body)
    if (json.optInt("code") != 200) return

    var data = json.optJSONObject("data")
    if (data == null) return

    var city = data.optString("city")
    var weather = data.optString("weather")
    sendText(getTargetTalker(), "${city} ${weather}")
})
```

## Hook pattern

Hook registration belongs in `onLoad()`, teardown in `onUnload()`.

```beanshell
var hookRef = null

void onLoad() {
    var method = com.tencent.mm.ui.MoreTabUI.class.getDeclaredMethod("onResume")
    hookRef = hookBefore(method, param -> {
        log("onResume Before")
    })
}

void onUnload() {
    if (hookRef != null) {
        unhook(hookRef)
        hookRef = null
    }
}
```

Prefer built-in helpers like `hookBefore`, `hookAfter`, `hookReplace`, `findClassList`, and `findMemberList` before lower-level reflective code.

## When to use which API

- auto-reply or command bot: `onHandleMsg` + `sendText`
- User-initiated queries, search, generation, and tool actions: prefer `onClickSendBtn`
- welcome/leave notice: `onMemberChange`
- friend auto-accept: `onNewFriend` + `verifyUser`
- remote content fetch: `get` or `post`
- image/media fetch then send: `download` + `sendImage` or `sendVoice`
- compatibility-sensitive feature: branch on `hostVerCode` or `hostVerClient`
- UI interception or deep app behavior: hook APIs in `onLoad`

## Common mistakes

- writing compiled Java class syntax instead of script-style top-level functions
- forgetting async callbacks for HTTP
- replying to the wrong conversation because `getTargetTalker()` was used instead of `msgInfoBean.getTalker()`
- handling other people's messages by default when the plugin should be self-only
- adding hooks without unhooking them in `onUnload()`
