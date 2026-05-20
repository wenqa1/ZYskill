# common host plugin references

This directory is for host-agnostic guidance shared across WA, QF, QS, and similar plugin hosts.

Store only material that is truly cross-host here:

- shared folder expectations
- shared BeanShell-style syntax rules
- shared enhanced BeanShell syntax such as lambda or list literals when the host family proves support
- shared HTTP caution rules only when they stay host-agnostic
- shared README conventions
- shared migration guidance for separating business logic from host bindings
- shared script-style authoring patterns that do not depend on one host's runtime names

Primary common reference files:

- `patterns-ai.md`: shared script model, style rules, import assumptions, host-agnostic data patterns, and cross-host porting rules
  - includes shared BeanShell syntax/runtime assumptions, default-enabled script features such as `var`/`val`/string templates/semicolon omission, enhanced syntax notes, and shared default-import guidance when the target hosts use the same underlying runtime family

Do not place host-specific callback names, helper methods, object fields, or version notes here.
