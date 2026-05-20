# wa host references

This directory contains WA plugin reference notes for structure, globals, callbacks, and AI-oriented host API summaries.

## Reference files

- `repo-layout.md`: required files, metadata keys, runtime notes
- `globals.md`: concise documented global fields
- `callbacks.md`: concise lifecycle, message, send-button, and event callbacks
- `plugin-patterns.md`: minimal template, common helpers, HTTP, hooks, and message handling
- `reference-ai.md`: single-file AI reference for layout, globals, callbacks, structures, and methods

## Usage guidance

- Read `repo-layout.md` first when creating a new plugin folder.
- Read `callbacks.md` before choosing an entry callback.
- Read `globals.md` before introducing manual Android or reflection logic.
- Read `plugin-patterns.md` when you need a minimal starting template or helper usage pattern.
- Read `reference-ai.md` when you want one searchable file with exact field names, method names, and callback signatures.
