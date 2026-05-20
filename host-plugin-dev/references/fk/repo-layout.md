# fk repo layout

## Required path and files

FK plugins are stored under:

```text
/storage/emulated/0/Android/media/com.tencent.mm/FkWeChat/Plugin/
```

Each plugin is centered around two core files:

```text
<plugin-name>/
├─ main.java
└─ info.prop
```

Optional files may live beside them when the plugin needs local assets or split modules.

## Core files

- `main.java`: plugin entry script
- `info.prop`: plugin metadata in `key=value` format

## Metadata format

Documented minimum keys:

```properties
name=test
author=雲上升
version=1.0.0
desc=测试
```

Field meanings:

- `name`: plugin name
- `author`: author name
- `version`: plugin version
- `desc`: optional description

## Runtime notes

- FkWeChat uses a modified BeanShell-style engine.
- The runtime documents support for string templates.
- The runtime documents automatic semicolon insertion.
- `loadJava(...)` can be used to split logic into additional `.java` modules in the same plugin directory.

## Authoring guidance

- Default to one plugin directory per plugin.
- Keep `info.prop` minimal.
- Keep files directly under the plugin directory unless the plugin itself loads extra modules or assets.
- When the workspace already contains an FK plugin collection, match the local naming style before creating new folders.
