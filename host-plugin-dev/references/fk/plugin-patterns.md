# fk plugin patterns

## Minimal files

Default standalone layout:

```text
<plugin-name>/
├─ info.prop
├─ main.java
└─ helper.java
```

`helper.java` is optional and shown only because FkWeChat explicitly supports `loadJava(...)` for modularization.

`info.prop`

```properties
name = [插件名称]
author = [作者]
version = 1.0.0
desc = [插件说明]
```

`main.java`

```java
onLoad() {
    log("plugin loaded: " + pluginName)
}

onMsg(msg) {
    var content = msg.content
    var talker = msg.talker
    if (content == "早上好") {
        sendText(talker, "早上好！")
    }
}
```

## Metadata

- `name`: plugin name
- `author`: author name
- `version`: plugin version
- `desc`: plugin description

## Style rules

- Apply the shared rules in `references/common/patterns-ai.md` first.
- Prefer small top-level functions over class-wrapped Java style.
- String templates are available, but when compatibility is uncertain inside a specific workspace, conservative concatenation is still acceptable.
- Use `loadJava(...)` when the plugin grows beyond a single clear script.

## Load helpers

Documented dynamic loading helpers:

- `loadDex(path)`
- `loadJar(path)`
- `loadJava(path)`
- `loadAar(path)`

Typical usage:

```java
loadJava("test")
```

## Common send helpers

Documented send helpers include:

- `sendText(wxid, text)`
- `sendImage(talker, path)`
- `sendImage(talker, path, isRaw)`
- `sendFile(wxid, path)`
- `sendEmoji(wxid, md5)`
- `sendCard(wxid, xml)`
- `sendXml(wxid, xml, type)`
- `sendMusic(wxid, title, desc, musicUrl, thumbPath, lyric)`
- `sendMedia(wxid, mediaObj, appId)`
- `sendNetScene(netScene)`

## Preferred interaction patterns

- Passive auto-reply or automation: `onMsg(msg)`
- User-triggered action on a specific message: `onMsgMenu(msg)` with `addMenuItem(...)`
- Heavy logic split into modules: `loadJava(...)`

## Message handling pattern

```java
onMsg(msg) {
    var content = msg.content
    var talker = msg.talker
    if (content == null) return

    if (content == "发图") {
        sendImage(talker, "/sdcard/test.jpg")
    }
}
```

## Menu action pattern

```java
onMsgMenu(msg) {
    addMenuItem("语音转文字", "", () -> {
        sendText(msg.talker, "处理中")
    })
}
```

## Verification guidance

- Verify callback names against the documented FK callback files in this directory.
- Verify `msg` field access against the documented `Msg` structure files before using uncommon fields.
- Verify file paths are absolute where send helpers expect absolute local paths.
- If using CGI or CDN interception, confirm the plugin really needs low-level interception instead of a higher-level message flow.
