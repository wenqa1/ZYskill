---
name: host-plugin-dev
description: Build or modify WA, QF, QS, FK, and similar host-script plugins. Use when creating, porting, debugging, reviewing, or extending plugins built around `info.prop`, `main.java`, host callbacks, helper APIs, and host-specific message or object structures.
---

# host plugin dev

Use this skill for WA, QF, QS, FK, and similar host-script plugin development, not for Codex `.codex-plugin/plugin.json` plugins.

## Core policy

- Identify the target host first. Do not start from a generic template.
- Treat same-host docs and same-host working plugins as the source of truth.
- Do not copy callback names, globals, metadata keys, helper names, or path assumptions across hosts.
- Create only the files the target host or current workspace actually expects.

## What to build

When creating or editing a plugin, use the verified host shape below.

- `wa`
  - runtime files: `info.prop`, `main.java`
  - workspace extras sometimes used: `README.md`, `config.prop`
  - metadata keys: `name`, `author`, `version`, `updateTime`
  - common entry callbacks: `onHandleMsg`, `onClickSendBtn`, `onLoad`, `onUnload`
- `fk`
  - runtime files: `info.prop`, `main.java`
  - optional extra module: `helper.java`
  - metadata keys: `name`, `author`, `version`, `desc`
  - common entry callbacks: `onMsg`, `onMsgMenu`, `onLoad`, `onUnload`
- `qf`
  - runtime files: `info.prop`, `main.java`, `desc.txt`
  - metadata keys: `id`, `pluginName`, `author`, `versionCode`
  - common entry callbacks: `onMsg`, `getMsg`, `chatInterface`, `unLoadPlugin`
- `qs`
  - runtime files: `info.prop`, `main.java`, `desc.txt`
  - optional preview assets: `images/icon.png`, `images/preview1.png`, `images/preview2.png`
  - metadata keys: `name`, `type`, `version`, `author`, `id`, `date`, `tags`
  - common entry callbacks: `onMsg`, `getMsg`, `onCreateMenu`, `onClickFloatingWindow`, `onUnLoad`

Notes:

- Generate only the files required by the verified target host.
- Do not add `README.md`, `config.prop`, `desc.txt`, `helper.java`, or preview assets unless that host or workspace expects them.
- If the user is working inside an existing shared plugin repo, preserve that repo's outer layout instead of flattening it.
- If no outer layout is required by the current workspace, create only the plugin-name directory and put plugin files directly inside it.

Read these references as needed:

- Common cross-host guidance belongs under `references/common/`.
  - ai patterns: `references/common/patterns-ai.md`
- WA-specific guidance belongs under `references/wa/`:
  - overview: `references/wa/README.md`
  - repo layout: `references/wa/repo-layout.md`
  - globals: `references/wa/globals.md`
  - callbacks: `references/wa/callbacks.md`
  - templates and patterns: `references/wa/plugin-patterns.md`
  - ai reference: `references/wa/reference-ai.md`
- FK-specific guidance belongs under `references/fk/`:
  - overview: `references/fk/README.md`
  - repo layout: `references/fk/repo-layout.md`
  - globals: `references/fk/globals.md`
  - callbacks: `references/fk/callbacks.md`
  - templates and patterns: `references/fk/plugin-patterns.md`
  - ai reference: `references/fk/reference-ai.md`
- QF-specific guidance belongs under `references/qf/`:
  - overview: `references/qf/README.md`
  - repo layout: `references/qf/repo-layout.md`
  - globals: `references/qf/globals.md`
  - callbacks: `references/qf/callbacks.md`
  - templates and patterns: `references/qf/plugin-patterns.md`
  - ai reference: `references/qf/reference-ai.md`
- QS-specific guidance belongs under `references/qs/`:
  - overview: `references/qs/README.md`
  - repo layout: `references/qs/repo-layout.md`
  - globals: `references/qs/globals.md`
  - callbacks: `references/qs/callbacks.md`
  - templates and patterns: `references/qs/plugin-patterns.md`
  - ai reference: `references/qs/reference-ai.md`

Truth-source order:

1. host-specific docs already present in the workspace
2. nearby working plugins for the same host
3. bundled references under `references/<host>/`
4. direct user-provided examples or API notes
5. another host's references only for clearly shared scripting idioms, never for host-specific names

## Workflow

1. Identify whether the task is:
   - creating a new plugin
   - modifying an existing plugin
   - adapting a plugin across repos, hosts, or layouts
   - debugging callback, API, or hook behavior
2. Identify the target host explicitly: `wa`, `qf`, `qs`, `fk`, or another compatible host. Do not guess from memory if the workspace can tell you.
3. Determine whether the workspace expects a bare plugin folder or a repo-specific outer layout.
4. If editing inside a shared plugin repo, inspect nearby plugins for the same host before editing.
5. Choose the truth source for callback names, helper methods, and object fields:
   - same-host docs in the workspace first
   - same-host working plugins second
   - bundled host references under `references/<host>/` next
   - another host's docs only when checking syntax patterns that are clearly shared
6. Open the host's `reference-ai.md` before inventing code shape, callback names, or helper names.
7. Keep `info.prop` minimal and consistent with the verified target host schema. Do not reuse metadata keys from another host.
8. Implement logic in `main.java` using the smallest callback surface that solves the task.
9. Generate `README.md` only when the target host or current workspace expects it. When generated, use this non-technical structure:
   - what this plugin does
   - how to trigger or use it
   - what the user will see after it succeeds
   - simple notes or cautions in plain language
10. Prefer high-level built-in helpers before reaching for reflection, hooks, or low-level host internals.
11. For network tasks, verify the target host's HTTP model first. Some hosts document async callback helpers, while others document synchronous `String`-returning helpers.
12. When the response is JSON, prefer `org.json` when the target host examples already use it.
13. For plugin configuration, prefer built-in config APIs before using raw `File`, `Properties`, streams, or manual host config parsing.
14. For file output, use the target host's documented plugin-owned path and temp/cache path patterns instead of assuming one global path API exists across hosts.
15. Add short comments only where behavior is non-obvious.
16. Do not invent callback names, globals, object fields, or helper methods. If a symbol is not covered by verified docs or visible in same-host examples, inspect the source docs or example plugins first.
17. If the user asks for hooks, DexKit, reflection, or cross-host porting and the target host API is not locally verified, stop and ask for one canonical same-host example or API doc before implementing the risky part.

## Default implementation rules

- Treat `main.java` as script code, not a compiled Java class.
- Read `references/common/patterns-ai.md` for cross-host script conventions, then override with same-host rules where needed.
- Follow the target host's proven script idioms before applying style preferences from another host.
- Match same-host conventions for `val`/`var`, explicit types, imports, semicolons, and string templates.
- In this shared runtime family, default to `val` for locals and use `var` only when the variable will be reassigned.
- Keep top-level callback functions and simple guards unless the target host examples show a different structure.
- Filter early in message callbacks:
  - default to handling only self-sent messages to avoid overreach
  - use the same-host equivalent of `isSend()` to avoid processing other people's messages unless the user explicitly wants that
  - branch by same-host message helpers like text/image/mention/group checks when they exist
- Use the target host's documented reply destination fields, chat target getters, and chat-type fields instead of assuming WA naming.
- If a current-chat getter may be absent or unstable in the target host, prefer light defensive checks instead of assuming it is always valid.
- Match the target host's documented HTTP and download model exactly. Do not rewrite synchronous helpers into WA-style async callbacks, and do not rewrite async helpers into direct-return code.
- Prefer built-in config read/write helpers over native file IO for normal plugin settings when that host documents them.
- For hooks or long-lived registrations, use the target host's documented load/unload lifecycle pair.
- For compatibility-sensitive logic, log or branch on the target host's documented version globals when they exist.
- Treat same-host docs and existing same-host plugins as the source of truth for available methods and callback names.
- Do not silently map one host's callback names, globals, or helper names onto another host.

## Callback selection

Choose callbacks from the target host's verified API, not by analogy.

- passive message automation
  - `wa`: `onHandleMsg`
  - `fk`: `onMsg`
  - `qf`: `onMsg`
  - `qs`: `onMsg`
- send-button text preprocessing
  - `wa`: `onClickSendBtn`
  - `qf`: `getMsg`
  - `qs`: `getMsg`
- per-message user action
  - `fk`: `onMsgMenu`
  - `qf`: `addMenuItem(...)` callback
  - `qs`: `onCreateMenu(...)`
- one-time setup and cleanup
  - `wa`: `onLoad` / `onUnload`
  - `fk`: `onLoad` / `onUnload`
  - `qf`: runtime lifecycle plus `unLoadPlugin`
  - `qs`: `onLoad` / `onUnLoad`

If the needed behavior is not obvious from this map, open the host's `reference-ai.md` and callback reference before writing code.

## Editing guidance

- When the user asks for a new plugin, generate `info.prop` and `main.java` together.
- Generate `README.md` only when the target host or current workspace expects it, unless the user explicitly asks for it.
- Keep `README.md` free of implementation details such as APIs, JSON parsing, hook internals, or code structure unless the user explicitly asks for technical documentation.
- Default `README.md` structure for beginner-facing plugins:
  - what this plugin does
  - how to trigger or use it
  - what the user will see after it succeeds
  - simple notes or cautions in plain language
- For user-initiated query plugins, prefer the target host's explicit user-action callback when it has one, over passive text listeners unless the user explicitly asks for text-message listening.
- When the user asks for a fix, read the current `main.java` first and preserve existing callback structure unless it is clearly wrong.
- When adapting a plugin across repos or layouts, compare the closest existing implementation first instead of assuming the structure from memory.
- Keep repository naming and file placement stable. Do not silently convert this plugin format into another framework.
- If the user did not explicitly authorize processing other people's messages, keep the plugin limited to self-sent message handling.
- Before using an uncommon method, verify it exists in same-host docs or a nearby same-host plugin example. Do not guess method names from intuition.
- If the task is a cross-host port, separate the shared logic from the host bindings:
  - preserve the command parsing, business rules, and JSON handling where possible
  - rewrite callback names, helper method calls, and message/object field access per host

## Validation

- Verify the folder layout matches the current workspace expectation: bare plugin-name folder by default, or repo-specific outer folders when already required.
- Verify `info.prop` uses the verified target-host key set and casing.
- Verify `README.md`, `desc.txt`, `config.prop`, preview assets, or helper modules are present only when the target host or workspace expects them.
- Verify generated `README.md` matches the actual commands and settings behavior in `main.java`, follows the default beginner structure, and stays non-technical.
- Verify message-triggered features do not process other people's messages unless the user explicitly asked for that behavior.
- Verify callback code uses same-host APIs documented in verified references or demonstrated in same-host plugin examples.
- Verify there are no guessed callback names, helper methods, or object fields unsupported by verified docs or same-host examples.
- Verify one host's names, metadata keys, or helper APIs did not leak into another host's plugin.
- Verify locals follow the shared default style: use `val` unless reassignment is actually needed, and use `var` only for mutable locals such as counters or accumulators.
- Verify shared default imports are not redundantly reintroduced when the target host already documents that runtime family behavior.
- Verify async code handles `null` in callbacks where failure is possible.
- Verify hooks are removable in `onUnload()` if added in `onLoad()`.
