---
name: gittar
description: Download repository snapshots and copy selected directories with @hulla/gittar, choosing refs, cache isolation, and update policies. Use when building template or skill downloaders or fixing cache/output handling; not for Git clone workflows or unrelated downloaders.
license: MIT
metadata:
  author: hulladev
  version: "0.3.0"
  package: "@hulla/gittar"
  reviewed-package-version: "0.0.4"
  reviewed-source: "5a3454d12d9df3fde89ef6b561cf47de1252f0da"
---

# Download snapshots with `@hulla/gittar`

Inspect the installed package and the tool's destination/update policy. This guidance was reviewed against 0.0.4. The implementation uses Bun APIs and shell utilities for filesystem work and tar extraction; do not assume that the package's JavaScript bundle runs unchanged under Node.

Use the default `gittar` export with a URL or config object. It returns file paths and snapshot metadata. `getTarUrl` only resolves a download URL; it does not download or try alternate branches.

## Separate download, cache, and output

The package downloads a complete repository archive for one ref and extracts the whole snapshot into its cache. It does not clone Git history. **`subpath` filters copying and the returned file list; it does not reduce network download size.** A separate small distribution repository can make this appropriate for skills or templates.

Set `outDir` separately from `cacheDir` when consumers will edit installed files. Without `outDir`, returned paths point into the reusable cache, and edits change future cache hits. Copying a selected directory puts its contents directly in `outDir`, without a surrounding subdirectory of the same name.

The package copies files; it does not install dependencies, merge local edits, or remove stale files. Keep overwrite/update decisions in the calling tool. Its returned `files` list uses default glob behavior and may omit dotfiles even when the directory copy included them; do not treat it as a complete installation manifest.

## Select refs and isolate caches

- `owner/repo` shorthand selects GitHub. Use an explicit `subpath` config field for directory selection; do not append a path to the shorthand and assume it will be selected.
- An explicit `branch` can select a branch, tag, or commit and prevents `main` to `master` download fallback. Prefer the config field for refs containing `/`; tree-URL parsing treats a single segment as the ref.
- The default cache key includes owner/repository, without host or ref. Supply a distinct `cacheDir` for each host/repository/ref so a cached snapshot cannot be mistaken for another source.
- Use absolute cache/output paths when returned paths must be absolute. `~/` expands, but other relative paths remain relative.

## Choose freshness semantics explicitly

| `update` | Existing cache | Cache miss |
| --- | --- | --- |
| `commit` (default) | Check remote branch commit and refresh when changed. | Download. |
| `always` | Download again; download failure rejects. | Download. |
| `never` | Reuse without checking the remote. | Download. |

`never` does not mean offline-only. The commit check uses Git branch lookup, can fail independently of archive downloading, and falls back to an existing cache when unavailable. In this release it is not general tag/commit freshness verification. Do not claim that a successful cached call proves current remote content, or that returned optional commit metadata verifies the archive's provenance. Use an explicit immutable ref with an isolated cache for reproducible input.

## Respect provider and failure boundaries

Recognized repository/tree URLs and SSH-style input still produce HTTPS archive requests. SSH syntax does not use SSH authentication. The reviewed config has no token or custom-header option. Inspect supported URL forms before promising private-repository, arbitrary self-hosted, or nested-group support.

Catch the awaited call at the tool boundary when translating errors into diagnostics: `URLError` covers source/download failures, and `FSError` covers filesystem failures. Neither class provides a transaction or automatic restoration of locally edited destinations.

## Focused documentation and verification

- [Cache, subpath, and copying policies](https://hulla.dev/docs/gittar/caching.md)
- [Options, URL forms, metadata, and errors](https://hulla.dev/docs/gittar/reference.md)

Verify the selected files and destination layout in an isolated directory. If freshness or source selection changes, exercise the relevant cache/ref case rather than relying only on a successful download. Reuse existing downloader checks and do not mutate a user's installation to test copying.
