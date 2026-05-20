# qs callbacks

Documented callbacks:

- `void onMsg(Object msg)` or `onMsg(MessageData msg)`: receive message
- `void onForbiddenEvent(String groupUin, String userUin, String opUin, long time)`: mute event
- `void onTroopEvent(String groupUin, String userUin, int type)`: join or quit event
- `void onClickFloatingWindow(int type, String uin)`: floating window open
- `String getMsg(String msg, String peerUin, int chatType)`: pure text send preprocessing
- `void onCreateMenu(MessageData msg)`: long-press message menu creation
- `void callbackOnRawMsg(Object msg)`: raw message callback
- `void onLoad()`: script loaded
- `void onUnLoad()`: script unloaded

## Menu helpers

- `addItem(String name, String callbackName)`
- `addTemporaryItem(String itemName, String callbackName)`
- `removeItem(String itemID)`
- `addMenuItem(String name, String callbackName)`

## Guidance

- Use `onMsg(...)` for passive automation.
- Use `getMsg(...)` only for pure text preprocessing.
- Use `onCreateMenu(...)` with `addMenuItem(...)` for per-message user actions.
- Use `onClickFloatingWindow(...)` and `addTemporaryItem(...)` for chat-context tools.
- Keep the exact unload spelling `onUnLoad()`.
