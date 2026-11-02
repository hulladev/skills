---
name: hulla-style
description: Compose classes and typed variants with @hulla/style, configure serialization and merging, or use @hulla/style-native for native style objects. Use when integrating these helpers or fixing styling composition; not for generic visual design or other variant libraries.
license: MIT
metadata:
  author: hulladev
  version: "0.2.0"
  package: "@hulla/style"
  reviewed-package-version: "0.5.0"
  reviewed-native-package: "@hulla/style-native 1.0.0"
  reviewed-source: "cab157173a357c074634855bce6e3efa6cfab8c4"
---

# Compose styles with `@hulla/style`

Inspect the application's installed version and shared style helper before adding configuration. This guidance was reviewed against core 0.5.0 and the native preset 1.0.0. Earlier core releases may lack generic value pipelines; check actual declarations instead of changing dependencies to fit an example.

## Establish one shared pipeline

Import `style` from `@hulla/style`, then export the `cn` and `vn` returned by `style(...)` from the application's shared helper module. Import those configured helpers in components; do not assume the core exports a preconfigured `cn` or create a new factory per render.

`cn` composes values; `vn` selects a named value and passes it through the same pipeline. They do not generate CSS, inject a stylesheet, or supply a design-token system. Preserve the app's actual styling backend and shared theme.

## Compose classes without assuming conflict resolution

The default class pipeline serializes conditional/container inputs and deduplicates exact class tokens. `cn('px-2', 'px-4')` retains both; it does not infer which Tailwind utility should win.

If the application needs Tailwind-aware conflicts, use a compatible `tailwind-merge` composer in its shared configuration. Treat that as an explicit dependency and behavior choice. Passing a caller class last expresses composition order, but does not make the default composer resolve competing CSS rules.

A custom composer receives serialized arguments in their original order. A custom serializer receives every input, including strings. In the default serializer path, strings go directly to the composer. Keep these roles separate; do not introduce custom serialization just to change merging.

## Model variants as focused choices

Declare flat maps near their component for choices such as size, tone, or state. Compose the base styles and selected maps with `cn` rather than copying base styles into every choice. The `$size`/`$tone` naming convention can distinguish selectors from props, but is optional.

Use `typeof selector.infer` in a type position for allowed keys. `.infer` is `undefined` at runtime; call `selector(key)` to obtain styles. Set optional prop defaults through JavaScript parameters or existing application logic before passing the key. `vn` does not choose defaults or provide another variant library's compound-variant/configuration API.

## Choose the native or custom value path when needed

For React Native, read [native style composition](references/native.md). Use the preset's identity serializer and native composer together; the web serializer would interpret style objects as class-condition maps. Do not add a CSS runtime to a native app merely to use these helpers.

For another non-string pipeline, inspect `StylePipeline<Input, Serialized, Output>`, `Serializer`, and `Composer` in the installed exports. Supply both hooks and preserve their type connection. Existing class pipelines may omit hooks and use the string defaults.

## Focused documentation and verification

- [Class composition](https://hulla.dev/docs/style/composition.md)
- [Variant inference and defaults](https://hulla.dev/docs/style/variants.md)
- [Custom merging](https://hulla.dev/docs/style/custom-merging.md)
- [Public types and signatures](https://hulla.dev/docs/style/reference.md)

Use the application's relevant type check and inspect affected visual states. If a merging rule changes, verify the actual competing utilities with that composer. If a public variant contract changes, retain meaningful type coverage rather than asserting source text or internal class names as a proxy for appearance.
