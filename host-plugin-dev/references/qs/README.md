# qs host references

This directory contains QS plugin reference notes for structure, globals, callbacks, and common authoring patterns.

## Reference files

- `repo-layout.md`: required files, metadata keys, preview asset layout
- `globals.md`: documented global fields
- `callbacks.md`: documented lifecycle, message, menu, floating-window, and group-event callbacks
- `plugin-patterns.md`: minimal template, common helpers, storage, HTTP, and file handling
- `reference-ai.md`: single-file AI reference for layout, globals, callbacks, message fields, and helpers

## Source basis

This QS reference is based on `/data/user/0/com.termux/files/home/插件/qs.md`.

## Usage guidance

- Read `repo-layout.md` first when creating a new script folder.
- Read `callbacks.md` before choosing an entry callback.
- Read `globals.md` before introducing manual Android or reflection logic.
- Read `plugin-patterns.md` when you need a minimal starting template or helper usage pattern.
- Read `reference-ai.md` when you want one searchable file with exact field names, helper names, and callback signatures.
