# Handler execution and client lifecycle

Read for query/mutation authoring, live UI state, authentication changes, optimistic updates, or retry handling. Confirm signatures against installed declarations.

## Restricted handlers versus application servers

Import schema-bound `query` and `mutation` from the generated `_generated/functions` module. A callback suffices for a simple declaration; the object form uses `input`, `handler`, and optional `output`. Input/output accept Standard Schema validators. Database field declarations still use `v` from `@hulla/db/schema`.

The service runs bundled handler code in a fresh restricted JavaScript process for each invocation and retry. It supports computation, promises, and compatible bundled validators, but not network, filesystem, process APIs, native modules, timers, dynamic imports, ambient time, or randomness. `Date.now()`, `new Date()`, and `Math.random()` are not available there. Ordinary application-server code retains its normal capabilities.

Keep email, payments, and other external work in the application's server code. Do not assume those effects join a database transaction or are safe to replay. Keep declaration modules free of top-level effects. Shared database helpers can accept `ReadDatabase`/`WriteDatabase` without becoming public declarations.

Await all started database work before returning. A database failure ends the transaction; catching it does not make it committable again. Output validation finishes before commit and a failure rolls back writes. Direct transactions run their callback once by default; an explicit supported retry policy can rerun it after confirmed conflict rollback.

## Calls, observation, and mutation confirmation

Calling a generated query or mutation starts it immediately and returns an awaitable handle. Repeated awaits reuse that execution. Awaiting a query gives a one-time read; subscription keeps it live. `query.subscribe(...)` begins observation directly. Release subscriptions when the owner is disposed, or use the matching framework binding to manage that lifecycle.

Bindings consume generated callables, not a newly invoked handle on every render. Check framework-specific access patterns: React/Vue use `useQuery`, while Svelte/Solid use `createQuery`; their snapshots and mutation state have different ref/getter/accessor shapes. Server-rendered `initialData` does not itself create a server subscription. Treat authorized SSR fallback and client identity as the documented contract describes; do not seed a global cross-user cache.

Query data is recursively readonly. Optimistic updaters return replacement data and must be pure because layers can replay. Awaiting a mutation returns the authoritative server result, never the predicted row. The core client is memory-based; it is not an offline database, persisted mutation journal, or reload-recovery mechanism.

An interrupted mutation confirmation enters `recovering` and checks the original receipt; it does not resubmit. `confirmation_timeout`, `confirmation_interrupted`, and auth/context retirement do not prove rollback. Direct server `commit_outcome_unknown` likewise does not prove failure to commit. Do not implement blind write replay from those errors. Read the installed receipt/error contract before exposing a retry action.

## Identity and credentials

Use generated runtime helpers and the existing provider's auth-change subscription. `setAuth({ key, expiresAt? })` selects the client's auth scope and retires previous state when it changes; it does not authenticate a caller or transport a token. The server must verify credentials and supply `ctx.identity`. Use public client credential/header settings for the actual request credentials.

An asynchronous `setAuth` update retires the previous scope while resolving. Await it before dependent calls. Switching identity or signing out retires old cached results, handles, subscriptions, and optimistic layers; attach observations for the new identity. Use the documented request-scoped setup on the server so one user's mutable auth state cannot leak into another request.

See [functions](https://hulla.dev/docs/db/functions.md), [runtime setup](https://hulla.dev/docs/db/runtime.md), [authentication](https://hulla.dev/docs/db/authentication.md), and [framework bindings](https://hulla.dev/docs/db/framework-bindings.md) for task-specific details.
