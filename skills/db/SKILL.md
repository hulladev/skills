---
name: db
description: Build applications with @hulla/db schemas, direct server access, generated queries/mutations, live clients, and framework bindings. Use when integrating the package or changing application database behavior; not for unrelated databases or developing the native engine.
license: MIT
metadata:
  author: hulladev
  version: "0.3.0"
  package: "@hulla/db"
  reviewed-package-version: "0.1.2"
  reviewed-source: "596b4251661b3298e1a07a4a41010e0c29c04d18"
---

# Build applications with `@hulla/db`

Inspect the installed core/binding versions, `db.config.ts`, schema, generated entrypoints, and existing authentication setup. This guidance was reviewed against 0.1.2. Check installed exports and matching documentation; the package is evolving and online examples may target a different revision. Preserve the application's framework and existing auth provider.

## Place code at its actual execution boundary

| Code | Responsibility |
| --- | --- |
| `@hulla/db/schema` declarations | Tables, field representations, constraints, indexes, and relationships. |
| `@hulla/db/config` configuration | Server connection/auth setup and public client settings. |
| Generated `_generated/server` | Direct database operations in the application's Node/Bun server. |
| Query/mutation declarations via `_generated/functions` | Restricted service-side computation with `ctx.db` and verified `ctx.identity`. |
| Generated `_generated/client/...` | Browser-safe callable contracts and subscription handles. |
| Matching framework binding | Component observation and lifecycle integration. |

Keep database credentials, server implementations, and server entrypoints outside the browser dependency graph. Generated browser bindings contain calling code and contracts, not handlers. Use the supplied generation/service flow rather than maintaining a manual function registry or a route per declaration. Modify authored inputs and regenerate; generated entrypoints are not the place to implement application handlers.

## Choose direct server access or declared functions

Use direct database operations in an ordinary application route, action, or job that owns authentication, authorization, and external integrations. Separate root operations commit independently; use `db.transaction(async tx => ...)` for one atomic group and use its scoped `tx` throughout. Direct operations do not automatically invoke declaration middleware or browser mutation receipt handling.

Use declared `query`/`mutation` functions when browser callers need generated typed calls and live results. Queries have a read-only consistent snapshot. Mutations run in one serializable transaction with bounded conflict retries. Enforce authorization in handlers or extensions using the server-verified identity; a browser cache key is not proof of identity.

Before authoring service handlers or changing retries, confirmation, or authentication, read [execution and client lifecycle](references/runtime-and-clients.md). The handler runtime cannot perform ordinary server I/O; moving a function into a declaration changes its capabilities.

## Author schemas and database operations deliberately

- Use `defineSchema`, `defineTable`, and `v` from `@hulla/db/schema`. Separate connection settings from table declarations. Keep field/ID types derived from declarations or generated type helpers instead of maintaining parallel row types.
- Nullability and omission differ: `.nullable()` allows null but does not make an insert field optional; add a default when omission should be allowed. An undefined patch leaves a field unchanged; null clears only a nullable field.
- Declare references, deletion policy, unique rules, and indexes explicitly. Relationships do not generate automatic joins, and ordering/filtering does not create an index.
- Direct CRUD builders are lazy and execute when awaited. Finish their modifiers before execution; repeated awaits reuse the operation. Generated browser handles instead start when invoked.
- Select bounded rows or pages for potentially large reads. `.unique()` returns one row or null and rejects multiple matches; `.limit(1)` remains an array.
- Update/delete require a predicate; `.where(true)` is an explicit all-row operation. A zero-row write is otherwise a valid outcome; use the installed `expectRows` API when a row-count invariant must be enforced before commit.
- Use declared storage representations/codecs for dates, integer modes, and application/storage conversion. Do not assume plain JSON preserves bigint or that annotating a type converts a stored value.

## Regenerate and evolve storage through supported commands

Read [schema changes and deployment](references/schema-and-deployment.md) when adding a field, changing a schema, provisioning a database, or selecting a deployment runtime. Generation, initialization, and migration have distinct effects; do not reset an existing database to make generated artifacts fit.

## Retrieve focused documentation

| Task | Official Markdown documentation |
| --- | --- |
| Choose execution/module ownership | [Mental model](https://hulla.dev/docs/db/mental-model.md) |
| Define tables and constraints | [Schemas](https://hulla.dev/docs/db/schemas.md) |
| Direct CRUD and transactions | [Database access](https://hulla.dev/docs/db/database.md) |
| Declare service-side functions | [Functions](https://hulla.dev/docs/db/functions.md) |
| Configure the generated service | [Runtime](https://hulla.dev/docs/db/runtime.md) |
| Connect auth scopes | [Authentication](https://hulla.dev/docs/db/authentication.md) |
| Subscribe and paginate | [Live queries](https://hulla.dev/docs/db/live-queries.md) |
| Add optimistic predictions | [Optimistic writes](https://hulla.dev/docs/db/optimistic-writes.md) |
| Bind UI state and SSR data | [Framework bindings](https://hulla.dev/docs/db/framework-bindings.md) |
| Choose field/wire representations | [Data types](https://hulla.dev/docs/db/data-types.md) |

## Verify the changed boundary

Run the consumer's generation/type checks and relevant existing integration checks. Exercise the meaningful changed behavior, such as authorization scope, rollback, subscription cleanup, or an additive migration, in a disposable environment when needed. An in-memory mock cannot establish PostgreSQL commit or recovery behavior. Do not run the package engine's full contributor test suite for an ordinary consumer edit.
