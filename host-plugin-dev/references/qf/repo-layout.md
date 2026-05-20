# qf repo layout

QF scripts use a `plugin` directory under the app data area.

## Required files

The documented minimum layout is:

```text
<script-name>/
├─ main.java
├─ info.prop
└─ desc.txt
```

- `main.java`: script execution entry
- `info.prop`: metadata config
- `desc.txt`: optional description file

## Metadata format

The documented keys are:

```properties
id=脚本唯一标识符
pluginName=脚本名称
author=作者
versionCode=版本号
```

Field meanings:

- `id`: unique script ID
- `pluginName`: script name
- `author`: author name
- `versionCode`: version number

## Runtime notes

- The runtime is described as Modern BeanShell.
- Java 8+ syntax is supported.
- Annotations are not supported.
- Global APIs and variables are available at script scope.
- `context` and `classLoader` can be used directly.

## Authoring guidance

- Default to `main.java + info.prop`, and include `desc.txt` unless the workspace clearly omits it.
- Use the exact `pluginName/versionCode` metadata style documented here.
- Keep one script directory per plugin.
- Keep metadata keys and casing exactly as documented in `info.prop`.
