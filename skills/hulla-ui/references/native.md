# Native composition

Read this when using the `react-native` target or adapting a web composition. Inspect the installed native component and theme helper before choosing props; these notes describe the reviewed registry, not every revision.

## Parts and events

- Use native events such as `onPress` and the component's native accessibility props. Do not pass web-only DOM or Tailwind props to native components.
- Native text needs text components. The reviewed Button composition uses `Button` with an explicit `ButtonText` child; a bare string is not the equivalent composition.
- Parts can require explicit matching state. In the reviewed Button, pass matching `variant` and `disabled` values to Button and ButtonText when changing them; the parent does not infer child props.
- Connect input names, labels, and descriptions explicitly using the installed field/input APIs. Do not assume a web label association exists on native.

## Theme and sizing

Use the installed `NativeThemeProvider` at the app boundary for app-wide mode or custom fonts. Inspect its actual exported props. Theme helpers replace web global CSS for this target; a CSS styling runtime or Expo is not inherently required by the component sources.

In the reviewed Button, `style` controls layout and the reserved press region; `contentStyle` controls the painted surface. Preserve usable touch targets and text scaling when making the visible control compact. Web and native size names do not guarantee equal dimensions.

## Interaction ownership

| Task | Native target behavior to inspect |
| --- | --- |
| Supplemental help | Tooltip has a visible tap-to-reveal control. Essential instructions must remain discoverable. |
| Contextual content | Popover uses a dismissible modal surface. |
| Selecting items/actions | Select and DropdownMenu use sheets. |
| Repeated row actions | SwipeActions includes a visible alternative to the gesture. |
| File attachments | FileUpload composes selected files; the app supplies the picker and transfer. |

Choose the available primitive for the task. Check registry availability instead of assuming web-only families such as Sidebar or syntax highlighters have native implementations.

Preserve dismissal, device back behavior, focus/screen-reader behavior, and scroll/gesture coexistence where affected. React Native Web previews render native components in a browser, but touch, system behavior, and text scaling still need the appropriate device verification.

See the [native consumer guide](https://hulla.dev/docs/ui/native.md) for concrete provider and composition examples matching the current documentation.
