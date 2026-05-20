# fk globals

Documented global fields:

- `pluginName`: plugin name
- `pluginPath`: plugin path
- `pluginAuthor`: plugin author
- `pluginVersion`: plugin version
- `hostVerCode`: host version code
- `hostVerName`: host version name
- `hostLoader`: host class loader
- `hostContext`: host Android `Context`
- `myWxId`: current account wxid

## Usage guidance

- Use `pluginName`, `pluginAuthor`, and `pluginVersion` for logs and diagnostics.
- Use `hostVerCode` and `hostVerName` for compatibility branches.
- Use `hostContext` only when host APIs do not already provide a higher-level helper.
- Treat `pluginPath` as the plugin-owned root path when resolving local files.
