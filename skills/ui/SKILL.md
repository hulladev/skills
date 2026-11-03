---
name: ui
description: Install and compose @hulla/ui source components in Astro, React, Solid, or React Native applications. Use when building screens with the registry, changing shared themes, or fixing component integration; not for unrelated UI work or authoring the registry generator.
license: MIT
metadata:
  author: hulladev
  version: "0.3.0"
  package: "@hulla/ui"
  reviewed-generator-version: "0.0.2-beta.1"
  reviewed-cli-version: "0.0.0-beta.2"
  reviewed-source: "b3a5e362352500a6f570e88a220587577d3da5f5"
---

# Compose applications with `@hulla/ui`

## Inspect the consumer first

Identify the application's framework, component destination, import aliases, theme entrypoint, and existing components. Read `.hulla/ui.json` when present to understand configured registry sources and installs. Check the registry revision and installed source before relying on current website examples.

`@hulla/ui` is a source-component registry and generator ecosystem. Applications install editable component files through the `hulla` CLI and import them from their own project. Do not assume a runtime import such as `import { Button } from '@hulla/ui'` exists. The `@hulla/ui` npm package supplies generator tooling for registry authors; installing it is not the consumer component setup.

This guidance covers Astro, React, Solid, and React Native targets. Availability and props depend on the selected registry revision and framework; do not invent missing framework targets or translate props by component name alone.

## Install only what the application needs

For an application already configured with the `hulla` CLI, add a missing component through `hulla ui add --framework <target> <component>`. Read the [CLI UI workflow](https://hulla.dev/docs/cli/ui.md) and the installed command's help before using setup or update options.

For a new setup, follow `hulla ui init` and the existing project's package manager. Inspect the resulting aliases, dependencies, support helpers, and theme configuration. Preserve existing configuration and local component edits when reviewing installation or update diffs.

Keep component source, framework helpers, and theme files from a compatible registry revision. Adding copied markup alone can omit required dependencies, parts, or behavior. Consumer applications own their installed source; they do not need a sibling registry checkout or generator build to render components.

## Compose from actual components

- Search installed components before building an interactive control. Read the closest primitive's exports and props, then use its public parts for the behavior it already supplies.
- Use the actual installed import paths. Framework targets can have different file layouts, exports, event names, state ownership, and composition contracts.
- Keep form semantics, labels, descriptions, focus behavior, keyboard interaction, and disabled states supplied by the real component. A custom appearance must preserve those behaviors.
- When a suitable primitive is absent, implement the required behavior using the project's shared tokens and conventions. Do not present a local imitation as an installed registry component.
- Examples and previews should render the same components the application uses. When displaying source, use the actual rendering file or clearly label a smaller illustrative composition.

## Put visual changes at the right layer

| Change | Preferred location |
| --- | --- |
| Columns, available width, placement, gaps | Page or composition wrapper. |
| Emphasis, size, disabled state | Existing component props and variants. |
| App-wide colors, typography, radius | Shared theme tokens and entrypoint. |
| New behavior reused by the app | A shared component or helper in the consumer. |

Use variants before adding per-page paint overrides. A one-off background or focus override can break the component's other states. For a deliberate design-system change, update shared tokens or the consumer's shared component rather than scattering equivalent overrides across screens.

For web targets, retain the installed global stylesheet and support utilities, imported through the application's shared entrypoint. Native components use style objects and theme helpers instead of the web CSS setup.

## Respect platform behavior

For React Native or a web-to-native adaptation, read [native composition](references/native.md). Match the task's semantics while using the native target's real props and parts; a web component name does not promise the same interaction or dimensions on native.

## Retrieve focused documentation

- [Components](https://hulla.dev/docs/ui/components.md): locate a primitive and its composition.
- [Composition and themes](https://hulla.dev/docs/ui/composition.md): decide between wrapper layout, props, and shared tokens.
- [CLI UI workflow](https://hulla.dev/docs/cli/ui.md): configure or update a consumer installation.
- [React Native](https://hulla.dev/docs/ui/native.md): native theme, parts, and interaction differences.

Prefer installed source when its revision differs from the website. If a required component or prop is unavailable, identify that limitation and choose a supported composition rather than assuming a newer API.

## Verify the composition

Run the consumer's relevant type check or build. For changed interactions, exercise the actual control and affected focus, keyboard, disabled, or form behavior. Check responsive layout and relevant theme states for visual changes. For native behavior, use the host's appropriate device checks; a browser preview alone does not establish device behavior. Scale validation to the change and reuse the application's existing checks.
