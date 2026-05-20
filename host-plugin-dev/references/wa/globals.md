# wa globals

## Documented global fields

- `hostContext`: host Android `Context`
- `hostVerName`: host version name
- `hostVerCode`: host version code
- `hostVerClient`: host client identifier
- `moduleVer`: module version
- `cacheDir`: plugin cache directory
- `pluginDir`: plugin directory
- `pluginId`: plugin identifier
- `pluginName`: plugin name
- `pluginAuthor`: plugin author
- `pluginVersion`: plugin version
- `pluginUpdateTime`: plugin update time

## Usage guidance

- Use `cacheDir` for temporary downloads, generated media, and conversion output.
- Use `pluginDir` for plugin-owned files, local resources, and config files.
- Use `pluginName`, `pluginAuthor`, and `pluginVersion` for logs and diagnostics.
- Use `hostVerName`, `hostVerCode`, and `hostVerClient` for compatibility branching.
- Use `hostContext` only when a higher-level host helper does not already solve the task.
