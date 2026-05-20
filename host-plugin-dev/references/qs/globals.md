# qs globals

Documented global fields:

- `myUin`: current QQ number
- `context`: QQ global `Context`
- `appPath`: script runtime relative directory
- `loader`: QQ class loader
- `pluginID`: current script ID

## Guidance

- Use `myUin` for self checks.
- Use `context` for Android access when required.
- Use `appPath` for script-relative file loading such as `load(appPath + "/dir/Utils.java")`.
- Preserve the documented `pluginID` casing.
