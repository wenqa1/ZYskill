# qf globals

Documented global fields:

- `context`: host app global `Context`
- `pluginId`: current script ID
- `classLoader`: QQ host class loader
- `pluginPath`: current script folder absolute path
- `myUin`: current logged-in QQ number

## Guidance

- Use `context` for Android access when no higher-level API exists.
- Use `classLoader` for host-class imports and reflection when required.
- Use `pluginPath` for plugin-owned files.
- Use `myUin` for identity-aware automation and self checks.
- Preserve the documented `pluginId` casing.
