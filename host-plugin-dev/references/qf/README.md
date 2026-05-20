# qf host references

This directory contains QF plugin reference notes for structure, globals, callbacks, and common authoring patterns.

## Reference files

- `repo-layout.md`: required files, metadata keys, runtime notes
- `globals.md`: documented global fields
- `callbacks.md`: documented lifecycle, message, menu, and group-event callbacks
- `plugin-patterns.md`: minimal template, common helpers, storage, and dynamic loading
- `reference-ai.md`: single-file AI reference for layout, globals, callbacks, message fields, and helpers

## Usage guidance

- Read `repo-layout.md` first when creating a new script folder.
- Read `callbacks.md` before choosing an entry callback.
- Read `globals.md` before introducing manual Android or reflection logic.
- Read `plugin-patterns.md` when you need a minimal starting template or helper usage pattern.
- Read `reference-ai.md` when you want one searchable file with exact field names, helper names, and callback signatures.
