# Native style values

Use this reference for `@hulla/style-native`. The reviewed preset requires core 0.5.x and React Native 0.81 or later before 1.0; check the installed package's published peer dependencies before choosing versions.

Configure a shared helper with `style(native)`, importing `style` from `@hulla/style` and the `native` object from `@hulla/style-native`. The preset contains an identity serializer and a native composer. It is not a function and is not a separate `adapter` option. Core still owns `cn`, `vn`, and variant inference.

## Preserve native composition semantics

- Pass style objects, style references, arrays, and conditional values; class strings and class-condition maps are not the native API.
- The native composer retains object and nested-array references and override order. It does not flatten, clone, deep-merge, or deduplicate native styles. Later active values override earlier ones according to React Native semantics.
- With no active top-level value, `cn` returns `undefined`; with one it returns that input; with several it returns an ordered array. Do not equate this with a zero-allocation or rendering-performance guarantee.
- Use the same shared helper pair for View, Text, and Image recipes. Each call retains its style type. Validate a recipe with the relevant native type, such as `satisfies TextStyle`, when helpful.
- Declare stable styles and variant maps outside rendering where appropriate. Use JavaScript defaults for optional variant props; `.infer` remains a type helper.
- React Native owns interaction callbacks. Call `cn` inside a `Pressable` style callback after obtaining `pressed`; the composer does not evaluate callback functions for you.

The preset does not own themes, animations, gestures, text scaling, or component sizing. With installed `@hulla/ui` native components, use their theme provider/helpers and public style props. In the reviewed Button, `style` controls the press/layout region while `contentStyle` controls its painted surface.

See [the native styling guide](https://hulla.dev/docs/style/native.md) for focused examples and current package requirements.
