# Schema changes and deployment

Read when generating artifacts, provisioning or migrating storage, or choosing the service host. Match commands and supported hosts to the installed package revision.

## Separate build-time and database effects

- Author tables in the schema and connection/auth/public client settings in `db.config.ts`.
- `hulla-db generate` emits schema-bound entrypoints and browser callables. Generation and importing the generated modules do not start a listener or automatically change storage.
- Initialization provisions/verifies storage; repeating it preserves matching data. It does not reset an incompatible schema or silently migrate it.
- `hulla-db dev` can manage its own local PostgreSQL and regenerate/restart on changes. An explicit server URL or `DATABASE_URL` selects external storage; that database is not automatically initialized or migrated by development mode.
- Production serving uses prepared artifacts. Deploy compatible generated server and browser code together.

Check existing configuration before running a database-changing command. For application migrations, use `hulla-db migrate ... --check` to inspect the supported change plan. The apply command has no confirmation prompt. Apply it only to the target authorized by the task.

## Stay within the migration contract

The reviewed migration workflow supports a defined additive subset: appended tables/references, nullable or constant-defaulted new fields, and field reordering without changing definitions. Removing/renaming fields, changing existing representations/defaults/nullability, and changing existing indexes or uniqueness policies are outside that subset. A required new field without a default is rejected even for an empty table.

Use the documented upgrade procedure for the installed version. A production upgrade requires stopping writers, a recoverable backup, migration, regeneration/deployment of matching artifacts, and restarting the application/service. Do not describe it as rolling mixed-schema deployment or invent a force-reset/down-migration flag. Owned local development can apply supported changes on save while preserving rows; external/production databases need the explicit workflow.

If confirmation of migration commit is interrupted, inspect the plan again before retrying; do not reset data or replay application mutations to resolve uncertainty. Changes outside the supported subset need an explicit data transformation/recovery design.

## Deploy supported artifacts

The reviewed npm package includes native executables for macOS arm64 and Linux x64/glibc. Its generated direct-server entrypoint requires Node 22.18+ or Bun. Browser-safe callables can run in a browser, but that does not make the native database service deployable to every edge/serverless runtime. Check the host, executable support, and connection lifecycle in the deployment guide.

Keep provisioning credentials and database URLs on the server. Only public client settings belong in generated browser output. Regenerate when those build-time settings change; use the configured service URL/proxy instead of handwiring generated internal adapters.

See [code generation](https://hulla.dev/docs/db/code-generation.md), [schemas](https://hulla.dev/docs/db/schemas.md), and [deployment](https://hulla.dev/docs/db/deployment.md). The package repository maintains the detailed [schema migration procedure](https://github.com/hulladev/db/blob/596b4251661b3298e1a07a4a41010e0c29c04d18/docs/schema-migrations.md) for the reviewed source revision.
