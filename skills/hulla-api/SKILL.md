---
name: hulla-api
description: Build or change application APIs with @hulla/api 2.x, including shared contracts, server handlers, typed clients, transports, and framework adapters. Use when integrating the package or reviewing its use in an application; not for unrelated HTTP APIs or developing the package internals.
license: MIT
metadata:
  author: hulladev
  version: "0.1.0"
  package: "@hulla/api"
  package-major: "2"
  reviewed-package-version: "2.0.0"
  reviewed-source: "08a3c6db77f0a12496f039453361f08b8dcd940e"
---

# Use `@hulla/api` in applications

## Establish the actual API

Inspect the application's manifest, lockfile, existing contract, and client/server modules. This guidance targets 2.x. For another major, use documentation matching that release; do not silently migrate the application to fit this skill.

Check public exports and installed declarations before choosing helpers or adapter signatures. Read only the task-relevant documentation linked below. Online pages describe the current documentation and can differ from the installed package. Preserve the application's framework, schema library, data layer, and file layout unless the task requires changing them.

## Preserve responsibility boundaries

| Boundary | Put here |
| --- | --- |
| Shared contract | Paths, accepted inputs, response statuses and representations, declared errors, schemas, and codecs. |
| Server implementation | Handlers, services, authorization, server middleware, and request-scoped dependencies. |
| Client | `createClient()`, the selected transport, client context, and client middleware. |
| Host entrypoint | The framework adapter, native request context, and mounting. |

Share the contract with callers. Keep its dependency graph free of database connections, secrets, server implementations, and host-only imports. In-process clients import the implementation and must stay server-only; use a transport across browser, service, worker, or process boundaries.

The contract describes an API; it neither starts a server nor supplies handlers. The typed client is constructed from the contract; ordinary use does not require client code generation.

## Extend a contract through its public APIs

- Declare routes with `defineContract`, `route`, `router`, `request`, and `response` from `@hulla/api`. Use `@hulla/api/server` for `defineServer`, and `@hulla/api/client` for `createClient`.
- Route object keys form the typed call tree. The base path, router paths, and route path form the HTTP URL. Keep these concepts separate when adding a nested endpoint.
- Declare only inputs the endpoint accepts and every response status/representation its handler may return. Every path placeholder needs a corresponding route or inherited router parameter schema.
- Implement the selected contract with `defineServer(contract).implement(...)`. Match handler keys to contract keys rather than repeating routing paths. Return results with the handler's typed `response` helper.
- For a modular API, use documented contract selections and server composition rather than inventing a global router or client builder. A server must implement every route in its selected contract; a client may select a smaller subtree.

## Choose validation deliberately

Type-only declarations such as `request.json<Input>()` provide compile-time types without checking application data shape. Use a Standard Schema at an untrusted input boundary when runtime validation is needed, keeping the application's existing schema library.

Ordinary request schemas run on the server after decoding. Ordinary response schemas validate and transform on the server before serialization; the client does not rerun them. Use an explicit codec when both sides need an application value different from its wire representation, such as a `Date` encoded as an ISO string. Do not assume that a TypeScript annotation converts or validates a wire value.

## Mount and call using the right boundary

Choose the adapter for the existing host from the server or full-stack documentation. Verify that adapter's actual package exports and mounting signature; do not extrapolate one framework's API to another.

Use `fetchTransport` for HTTP and `inProcessTransport` only when client and implementation share a trusted JavaScript process. A browser can use its current origin; server-side Fetch generally needs an absolute URL or a framework's request-aware Fetch. Follow the host's request and cache semantics instead of introducing global request/session state.

By default, declared route responses and application errors return typed values. Narrow `result.status` before reading the corresponding body. A declared 404 is distinct from a network failure, invalid response, or unexpected exception. Respect an existing `errorMode: 'throw'` setup; inspect the installed error API before introducing that option.

The core client does not supply retries, caching, loading state, or deduplication. Keep those policies in the application's existing loaders, query library, or service functions. Pass cancellation through request options. Middleware calls `next()` at most once and returns its result; authorization must be enforced on the server.

## Read the focused reference

| Task | Official Markdown documentation |
| --- | --- |
| Decide module ownership | [Mental model](https://hulla.dev/docs/api/mental-model.md) |
| Add route declarations | [Contracts](https://hulla.dev/docs/api/core/contracts.md) |
| Implement handlers | [Servers](https://hulla.dev/docs/api/core/servers.md) |
| Compose clients and middleware | [Clients](https://hulla.dev/docs/api/core/clients.md) |
| Handle declared versus operational failures | [Errors](https://hulla.dev/docs/api/core/errors.md) |
| Convert application and wire values | [Value round trips](https://hulla.dev/docs/api/core/value-round-trips.md) |
| Mount in an existing server | [Server adapters](https://hulla.dev/docs/api/servers.md) |
| Place browser and server calls | [Hybrid rendering](https://hulla.dev/docs/api/full-stack/hybrid-rendering.md) |

Use [the documentation index](https://hulla.dev/llms.txt) to find the specific host, transport, or integration page when needed; avoid loading the full corpus for a focused change. If documentation is unavailable, work from installed declarations and existing working usage, and identify any unresolved API detail rather than inventing it.

## Verify the changed boundary

Use the application's relevant type check and existing request or integration checks. For a new endpoint, check the changed success/failure behavior and keep browser imports free of server dependencies. Add permanent coverage only for a meaningful public contract or observable failure risk; do not create a new testing stack just to validate usage guidance.
