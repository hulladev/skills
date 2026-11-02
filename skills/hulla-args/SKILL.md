---
name: hulla-args
description: Define and parse command-line input with @hulla/args, including flags, options, positional arguments, nested commands, and Zod validation. Use when integrating the parser or fixing its use in a CLI; not for unrelated argument parsers.
license: MIT
metadata:
  author: hulladev
  version: "0.2.0"
  package: "@hulla/args"
  reviewed-package-version: "0.0.4"
  reviewed-source: "674f1f70428c12cbd527bb69a75c6ca8d164a024"
---

# Parse CLI input with `@hulla/args`

Inspect the installed version, parser declaration, entrypoint, and runtime argv before changing behavior. This guidance was reviewed against 0.0.4; use the installed declarations and matching documentation for other releases.

## Keep parsing separate from command execution

Use `parser` with `flag`, `option`, `positional`, `sequence`, `infiniteSequence`, and `command` from `@hulla/args`. Parsing returns structured results; it does not execute handlers, print help, or set the process exit code. Keep execution and presentation policy in the application entrypoint.

Remove the runtime prefix exactly once: pass Node/Bun `process.argv.slice(2)` with default indexes, or the full argv with `settings.startIndex: 2`. Other runtimes or custom arrays can already contain only user arguments. `.parse()` reads the full supplied array by default.

## Match declarations to the input shape

- Use `flag` for a boolean switch, `option` for a named value, and `positional` for a value identified by order.
- Use separate positionals for independent values. A `sequence` is detected only when its entire positional group is present; incomplete input can become an unhandled-argument error.
- Use `infiniteSequence` for remaining positional values. Avoid assuming another parser's rest-argument or command syntax exists here.
- Long aliases include the declared name. A custom short alias replaces the default short form. Inspect collisions when introducing aliases; combined short forms require `sharedDash: true`.
- Commands remain keyed by their canonical name even when selected through an alias. Check each nested command's `detected` flag before running its behavior.
- Root and command arguments are separate by default. Use `mergeArgs` only when shared visibility is intended. `keepOnlyDetected` can remove omitted entries entirely; do not assume every declared property exists under that setting.

## Distinguish omission from validation

Read the result through `.arguments.<name>.value` and `.detected`. An omitted option or positional has `detected: false` and `value: undefined`; its schema is not invoked. A Zod `.default()` or `.min()` therefore does not supply a fallback or require a missing argument. Check presence explicitly, then apply the application's fallback or missing-input error.

Flags validate a boolean even when absent: their schema receives `false`, or `true` when detected. Use a coercing schema when a supplied text value must become a number or another application type. Catch `ParserError` at the CLI boundary to choose diagnostics and an exit status; allow unrelated failures to propagate according to the application's existing error policy.

## Preserve case-sensitive values deliberately

The default `caseSensitive: false` can lowercase the value inside `--name=Value` before validation. Space-separated values and positionals preserve their spelling, and `raw` retains the source token. For a new CLI accepting paths, identifiers, or secrets where casing matters, use `caseSensitive: true`; this also makes argument names case-sensitive. For an existing CLI, review that compatibility change instead of silently altering matching rules.

## Focused documentation and verification

- [Argument shapes](https://hulla.dev/docs/args/arguments.md)
- [Parser settings and argv slicing](https://hulla.dev/docs/args/options.md)
- [Nested commands](https://hulla.dev/docs/args/commands.md)
- [Validation and omitted input](https://hulla.dev/docs/args/validation.md)

Verify the changed command with supplied, omitted, and invalid input as relevant. If casing or argv handling changes, exercise the actual entrypoint with equals-form values and the runtime prefix. Reuse existing CLI checks rather than starting a process for every declaration.
