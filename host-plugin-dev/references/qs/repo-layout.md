# qs repo layout

## Required files

QS scripts require these files at minimum:

```text
<script-name>/
├─ main.java
├─ info.prop
└─ desc.txt
```

Optional preview assets:

```text
<script-name>/
└─ images/
   ├─ icon.png
   ├─ preview1.png
   └─ preview2.png
```

## Core files

- `main.java`: main script entry
- `info.prop`: script metadata in `key=value`
- `desc.txt`: script description for list display

## Metadata format

Documented required keys:

```properties
name=脚本名称
type=1
version=1.0
author=作者名
id=脚本唯一ID
date=2025-12-1
tags=群聊辅助,娱乐功能
```

Field meanings:

- `name`: script name
- `type`: fixed to `1`
- `version`: script version
- `author`: author name
- `id`: unique script ID
- `date`: `yyyy-M-d`
- `tags`: comma-separated labels

## Runtime notes

- No annotations.
- Java runtime is JDK 9.
- Java and Android standard classes can be used, but import them explicitly.
- Root-scope callbacks only.
- Global variables can be used directly.

## Authoring guidance

- Default to `main.java + info.prop + desc.txt`.
- Add `images/` only when the user wants icon or preview assets.
- Use the documented `name/type/version/author/id/date/tags` schema.
- Keep metadata keys and casing exactly as documented in `info.prop`.
