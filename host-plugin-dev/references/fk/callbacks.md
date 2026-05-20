# fk callbacks

Documented lifecycle and event callbacks:

- `onLoad()`: called after plugin initialization completes
- `onUnload()`: called when the plugin is unloaded
- `onMsg(msg)`: called when a message is received or sent
- `onMsgMenu(msg)`: called before the long-press message menu is shown
- `onCgiRequ(data)`: called when a CGI request is sent
- `onCgiResp(data)`: called when a CGI response is received
- `onCdnDownload(info)`: called for CDN download tasks
- `onCdnUpload(info)`: called for CDN upload tasks

## Menu extension

FkWeChat also documents:

```java
addMenuItem(title, path, action)
```

Parameters:

- `title`: menu label
- `path`: icon resource path, empty for default icon
- `action`: callback run when the menu item is clicked

## Guidance

- Use `onLoad()` for one-time setup.
- Use `onUnload()` for cleanup when a hook or long-lived resource is registered.
- Use `onMsg(msg)` for passive observation or auto-handling.
- Use `onMsgMenu(msg)` plus `addMenuItem(...)` for user-triggered actions on a specific message.
- Treat `onCgiRequ`, `onCgiResp`, `onCdnDownload`, and `onCdnUpload` as advanced interception callbacks and confirm intent before using them in new plugins.
