# wa callbacks

## Documented callbacks

- `void onLoad()`: plugin initialization hook
- `void onUnload()`: plugin unload hook
- `void openSettings()`: plugin settings entry
- `void onHandleMsg(Object msgInfoBean)`: message receive callback
- `boolean onClickSendBtn(String text)`: send-button interception callback
- `void onMemberChange(String type, String groupWxid, String userWxid, String userName)`: group member event callback
- `void onNewFriend(String wxid, String ticket, int scene)`: new-friend request callback
- `void onRecvPayMsg(Object payMsgBean)`: payment message callback

## Message handling guidance

- Use `onHandleMsg(...)` for passive listening, auto-reply, or inbound message processing.
- Use `onClickSendBtn(String text)` for user-initiated query, conversion, and tool-style plugins.
- Use `msgInfoBean` helpers such as `isSend()`, `isText()`, `isGroupChat()`, and `getTalker()` before acting on a message.

## Event and lifecycle guidance

- Use `onLoad()` for one-time setup, initialization, and hook registration.
- Use `onUnload()` for cleanup, unhooking, and releasing long-lived resources.
- Use `openSettings()` for plugin settings, help text, or status panels.
- Use `onMemberChange(...)`, `onNewFriend(...)`, and `onRecvPayMsg(...)` only when the plugin truly needs those host events.
