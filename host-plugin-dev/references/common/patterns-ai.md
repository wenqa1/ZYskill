# common ai patterns

This file stores cross-host scripting guidance that is safe to reuse across WA, QF, QS, FK, and similar host-script plugin environments.

Only keep material here when it is genuinely cross-host. If a rule depends on a specific host runtime, import behavior, callback name, or helper name, keep it in that host's reference instead.

## Shared script model

- plugin logic is usually written in top-level `main.java`
- metadata usually lives in `info.prop`
- callbacks are usually declared at file root, not wrapped in a compiled Java class
- host differences usually live in:
  - callback names
  - helper method names
  - message or object fields
  - runtime-specific extras such as README files, config files, or preview assets

## Shared style rules

- Prefer the exact style already proven in same-host docs or same-host plugins.
- If a host supports script-style locals such as `var` and `val`, prefer `val` by default and switch to `var` only when the local will be reassigned.
- If a host supports automatic semicolon insertion, follow the same-host examples instead of forcing semicolons everywhere.
- In this shared runtime family, string templates are enabled by default. Prefer them over verbose concatenation unless a specific host/workspace proves an incompatibility.
- Keep top-level callback functions and simple guard-first control flow unless the target host examples show a different pattern.
- Add comments only where behavior would otherwise be non-obvious.

## Shared BeanShell runtime assumptions

When the target hosts are known to run on the same BeanShell-derived runtime family, these rules can be treated as common unless the workspace proves otherwise:

- `main.java` is script code, not a compiled Java class
- file-root callback functions are preferred over class wrappers
- script-style locals such as `var` and `val` are available by default in this runtime family
- `val` may be used for immutable locals in this runtime family
- explicit semicolons may be omitted by default in this runtime family
- string templates such as `$name` and `${expr}` are available and preferred by default in this runtime family
- simple top-level helper functions may be declared directly in the script file
- lambda-style callback code may be used only where the same-host docs or examples already show that feature
- list literal syntax may be available in enhanced BeanShell runtimes, but only use it when the same-host docs or examples already show it
- prefer script-style direct field access patterns already proven in same-host examples over JavaBean getter assumptions

## Shared default imports

If the current host set is confirmed to share the same default-import behavior, prefer direct use of these packages without explicit `import` lines unless the workspace proves otherwise:

- `org.json`
- `android.widget`
- `android.view`
- `android.text`
- `android.os`
- `android.graphics`
- `android.content`
- `android.app`
- `java.util.stream`
- `java.util.regex`
- `java.util.function`
- `java.util`
- `java.net`
- `java.math`
- `java.io`
- `java.lang`

If a host or workspace proves a different import model, override this common rule in that host-specific reference.

## Imports and runtime assumptions

- Do not add imports just because standard Java would need them.
- Do not remove imports just because another host auto-imports those packages.
- For this shared WA/QF/QS/FK runtime family, treat the documented default-import list above as the default assumption unless the workspace proves a host-specific exception.
- For shared code extraction, isolate business logic first and keep host-runtime assumptions at the binding layer.

## Shared BeanShell authoring rules

- prefer top-level callbacks and helper functions over class wrappers
- prefer `val` for locals that do not need reassignment
- use `var` only for locals that will be reassigned
- prefer string interpolation over verbose concatenation for this runtime family
- omit redundant `import org.json.JSONObject;`, `import java.util.*;`, or similar lines when the package is already covered by shared default imports
- do not force standard Java ceremony such as `public class Main` or nested utility classes into script plugins
- keep script code compact and guard-first rather than building framework-like abstractions for small plugins

## Shared enhanced syntax

BeanShell-Android README examples document extra convenience syntax beyond plain Java. Treat these as optional shared capabilities for this runtime family:

- lambda expressions such as `() -> { ... }` or `arg -> { ... }`
- list literal syntax in the runtime flavor documented by the same-host examples
- relaxed script-style expression usage compared with compiled Java ceremony
- default-enabled string templates such as `"Hello, $name"` and `"sum=${1 + 2}"`
- default-enabled semicolon omission in normal script statements
- default-enabled immutable locals through `val`

Use these features conservatively:

- lambda syntax may be used when the current host/workspace already shows it
- only use list literal syntax when the same-host docs or working plugins already prove support
- if operator or literal support is uncertain, fall back to simpler same-host patterns instead of assuming the newest BeanShell feature set

Default-enabled examples for this runtime family:

```beanshell
val msg = "Hello World"
print(msg)

val name = "BeanShell"

val lang = "BeanShell"
val str1 = "Hello, $lang"
val str2 = "1 + 2 = ${1 + 2}"
val str3 = "price=\\$9"
```

## Message-handling guard pattern

Use the same-host equivalent of these checks when they exist:

- self-only guard first when the plugin should only react to the current user's own messages
- message-type guard before parsing content
- `null` and empty-string guards before branching on commands
- group/private branching only when behavior differs by chat type

## Async and data parsing

- Do not assume host network helpers are synchronous unless the same-host docs explicitly show that behavior.
- Check failure values exactly as documented in the target host.
- When the host already exposes JSON usage through `org.json`, prefer that over manual string slicing.
- When a host documents download helpers, prefer them over manual socket or stream code.

## Config and file patterns

- Prefer host-provided config helpers over manual parsing when they exist.
- Prefer host-provided temp/cache or plugin-owned paths over hard-coded external paths.
- Keep plugin-owned generated files inside the host's documented writable area.

## Cross-host porting rule

When porting a plugin across hosts:

1. keep command parsing and business rules if they are host-agnostic
2. replace metadata keys with the target host schema
3. replace callback names with the target host lifecycle and message entry points
4. replace helper calls with target-host equivalents
5. replace message or object field access with target-host structures
6. re-check imports, config helpers, and file paths for the new host
