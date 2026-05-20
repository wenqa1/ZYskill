# wa repo layout

This skill is based on the public repository `HdShare/WAuxiliary_Plugin`.

Default rule:

- only the final plugin directory matters
- only copy outer grouping folders when the current workspace already uses them

Important:

- The wa plugin itself only requires its own plugin directory.
- Outer grouping folders in shared repos are repository organization, not a plugin runtime requirement.

## Observed structure

The referenced public repo uses extra grouping folders before the actual plugin directory.

Shared repos may organize plugins like this:

```text
<repo-root>/
└─ <group>/
   └─ <author>/
      └─ <plugin-name>/
         ├─ info.prop
         └─ main.java
```

The key point is that only the final plugin directory is required by the plugin itself:

- `<plugin-name>/info.prop`
- `<plugin-name>/main.java`

## Metadata format

`info.prop` is lightweight. A representative example is:

```properties
name = 综合示例插件
author = Hd
version = 1.0.2
updateTime = 20260426
```

Default assumptions:

- `name` is the user-facing plugin name
- `author` should match the enclosing author folder unless the repo already deviates
- `version` is plugin version, not wa host version
- `updateTime` follows `YYYYMMDD`

## File responsibilities

- `info.prop`: plugin metadata
- `main.java`: plugin script entry and all callbacks
- `config.prop`: optional runtime config or plugin-owned data file

## Minimal standalone plugin layout

Outside a shared repo, the default generated layout should be:

```text
<plugin-name>/
├─ info.prop
├─ main.java
├─ README.md
└─ config.prop
```

## Authoring guidance

- Default to the final plugin directory only.
- If the plugin is being created standalone, generate only the plugin-name directory.
- If the current workspace already uses a shared repo layout, match the nearby grouping style instead of flattening it.
- Match the nearby plugin naming style instead of inventing a new hierarchy.
- If the user asks for a new plugin in a repo like this, place it into that repo's existing layout.
- If the user only asks for plugin code and no repo path, write code in wa format with a single plugin root directory.
