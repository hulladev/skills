---
name: control
description: Model application outcomes with @hulla/control Result values, narrowing, transformations, and tcf exception boundaries. Use when integrating the package or fixing synchronous/asynchronous result handling; not for generic control flow or other Result libraries.
license: MIT
metadata:
  author: hulladev
  version: "0.3.0"
  package: "@hulla/control"
  reviewed-package-version: "0.0.3"
  reviewed-source: "06ed2b405f5d203c91b825469843adca8542b1d6"
---

# Handle outcomes with `@hulla/control`

Inspect the installed version and existing function contracts before changing error handling. This guidance was reviewed against 0.0.3. Import public helpers and types from `@hulla/control`; preserve the application's error vocabulary and caller-facing contract.

## Choose the right construction boundary

| Situation | API and behavior |
| --- | --- |
| Application code already knows the outcome | Return `ok(value)` or `err(error)` explicitly. Error payloads can be domain objects. |
| A value may already be an Error | `result(value)` classifies it using `instanceof Error` by default, or a supplied `isError` guard. |
| An operation can throw or reject | Execute it inside `tcf({ try, catch, finally? })`. Its catch mapper must return an Error or a promise of one. |

`result` does not execute functions or catch promise rejection. `ok` and `err` wrap their payloads without catching anything. Cross-realm Error values need a suitable custom guard if default classification is insufficient.

## Narrow the branch, not the payload

Use `isOk()` or `isErr()` before reading `.value` or `.error`. Use `match(onOk, onErr)` when both branches should produce one value. Branch identity remains meaningful when success/error payload types overlap or contain falsy/undefined values; a payload truthiness check cannot replace it.

`pair()` returns `[value, undefined]` or `[undefined, error]`. Use branch guards when payload values make tuple-based detection ambiguous.

**`unwrap()` returns the original success value or error payload unchanged. It never throws merely because the branch is Err.** Do not apply the unwrap semantics of another Result library here.

## Transform without inventing recovery semantics

`map` transforms only success; `mapErr` transforms only failure. Both preserve the active branch and tag. Returning another Result nests it as a payload; mapping does not flatten it or switch branches. Use explicit narrowing/matching when the next operation can choose a new branch.

Callback exceptions and promise rejections propagate; transformations do not automatically become Err values. Use `tcf` at an operation boundary that needs exception conversion. Keep custom tags consistent with the declared Result type and use the branch guards rather than hardcoding the default tags.

## Consume asynchronous work at the correct layer

Await `tcf(...)` whenever any of its callbacks can be asynchronous. It waits for the operation, error mapping, and cleanup; errors from catch/finally propagate, and cleanup failure overrides an earlier outcome. Do not promise that it turns every failure into a Result.

A Result can contain a promise. Mapping promise payloads produces a Result with asynchronous payload, not a promise that resolves to a Result. Consume it with `await result.match(...)`, `await result.pair()`, or await the promise returned by `unwrap()`. Awaiting the Result object itself does not resolve its payload. Await needed work before reporting success at an application boundary.

## Focused documentation and verification

- [Results and branch handling](https://hulla.dev/docs/control/results.md)
- [Throwable operation boundaries](https://hulla.dev/docs/control/attempts.md)
- [Mapping](https://hulla.dev/docs/control/chaining.md)
- [Asynchronous payloads](https://hulla.dev/docs/control/async.md)

Use relevant caller type checks and existing behavior tests. For an error-handling change, verify the intended failure route and whether a throw/rejection propagates or becomes a Result. Keep tests focused on observable caller behavior, not rechecking the library's constants.
