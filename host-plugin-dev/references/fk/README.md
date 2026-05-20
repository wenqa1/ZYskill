# fk host references

This directory contains FK plugin reference notes for structure, globals, callbacks, and AI-oriented host API summaries.

## Reference files

- `repo-layout.md`: plugin storage path, `main.java`, `info.prop`, metadata keys
- `globals.md`: concise documented global fields
- `callbacks.md`: concise lifecycle, message, CGI, CDN, and menu callbacks
- `plugin-patterns.md`: minimal template, common globals, send helpers, load helpers
- `reference-ai.md`: single-file AI reference for layout, globals, callbacks, structures, and methods

## Usage guidance

- Prefer the curated summaries first when implementing or reviewing plugins quickly.
- Read `reference-ai.md` when you want one searchable file with exact host-provided names, parameter order, and structure fields.
- Keep callback names, globals, and structure fields aligned with the documented FK files in this directory.
