# Agent skills for `@hulla/*`

Consumer skills for building applications with `@hulla/*`. Each skill teaches package-specific architecture and usage decisions, with links to focused official documentation. This repository contains instructions and references, without package source code or build artifacts.

## Available skills

| Skill | Use it for | Compatibility |
| --- | --- | --- |
| [hulla-api](skills/hulla-api/SKILL.md) | Shared contracts, server implementations, typed clients, and host adapters. | `@hulla/api` 2.x; reviewed against 2.0.0. |
| [hulla-ui](skills/hulla-ui/SKILL.md) | Installing and composing source components, shared themes, and native interactions. | Astro, React, Solid, and React Native registries; reviewed against the `@hulla/ui` 0.0.2-beta.1 workspace and `hulla` CLI 0.0.0-beta.2. |

Compatibility records describe the guidance's target, not a guarantee for every future release. Each skill starts by inspecting the consumer's installed version or registry source. UI props and available components come from the installed source; matching names across frameworks do not imply identical APIs.

## Install

From your application directory, use the [skills CLI](https://github.com/vercel-labs/skills) to choose skills and supported coding agents:

```sh
npx skills add hulladev/skills
```

For Codex, install an individual skill into the current project:

```sh
npx skills add hulladev/skills --skill hulla-api -a codex
npx skills add hulladev/skills --skill hulla-ui -a codex
```

The external installer requires Node.js 22.20 or later. It also supports other agents, including Claude Code and Cursor; choose the appropriate agent in its interactive setup. These skills use the portable [Agent Skills format](https://agentskills.io/specification) and require no MCP server or executable helper.

For a reproducible starting point, install from the initial tagged snapshot:

```sh
npx skills add https://github.com/hulladev/skills/tree/v0.1.0/skills/hulla-api -a codex
```

Retain the selected skills and any installer lockfile in the consumer project. Review updates against your installed package versions before applying them; check your installer's behavior when updating a pinned source.

### Manual installation for Codex

Clone this small repository outside your application and check out a reviewed tag or commit. Copy the entire chosen skill directory into your application's `.agents/skills/` directory:

```text
your-app/
  .agents/skills/
    hulla-api/SKILL.md
    hulla-ui/
      SKILL.md
      references/native.md
```

Copy only the skills you use. Preserve any existing skill with the same name until you have reviewed the differences. Codex discovers skills in [supported project locations](https://learn.chatgpt.com/docs/build-skills); cloning this repository elsewhere does not activate them in your application.

These are consumer instructions. Repository contributor guidance belongs in the relevant package repository. Installation does not change package dependencies, application code, or project instruction files by itself; inspect the chosen installer's changes.

## Keeping guidance current

- Maintain each workflow here. Keep the detailed API reference and examples in the package documentation, and link to the relevant Markdown pages instead of copying entire manuals.
- Record the target version and reviewed source revision in skill metadata. Check the installed declarations and component source before applying online documentation, which can describe a newer release.
- Update affected skills when a package changes its public API, architecture, or consumer setup. Preserve guidance for incompatible package majors in tagged snapshots.
- Tag skills releases independently from package releases. `v0.1.0` is a skills release, not an `@hulla/api` or `@hulla/ui` version.
- Validate frontmatter and relative references, then try relevant prompts in an isolated consumer project. Record what was actually exercised; format validation alone does not establish behavioral reliability.

Useful evaluation prompts include adding a browser-callable API endpoint without importing server code into the browser, handling an expected not-found response, composing a settings form from installed UI components, and adapting a web composition to native components using their real props.

## License

[MIT](LICENSE).
