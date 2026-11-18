---
name: figcraft-design-system
description: Build or maintain a FigCraft design system. Use for reusable components, variants, instance properties, shared styles, and design tokens implemented as variables and modes.
---

# FigCraft design systems

Use the FigCraft MCP backend that holds the target document. `figcraft-headless` is the default for local files and new designs. Use the configured desktop backend, such as `figcraft-desktop`, when the user directs work to an attached document. Open an explicit input path with `open_file`, or locate a named open document with `doc.list` via `run_command`. Resolve any ambiguity or `opened.stale` before editing. Pass the resolved `document` and explicit layer IDs on tools that support them. Resolve IDs again when switching documents or backends.

Use `list_commands` with a narrow filter such as `component`, `style`, or `variable.bind` to confirm the installed command and parameter shape. Inspect the target subtree with `get_node` and check `component.list`, `style.list`, or `variable.list` as relevant before creating shared assets. Prefer reusing matches with the same purpose and type.

## Components and instances

- Build repeated UI as a main component with `component.create {ids:[frameId]}` and place linked copies with `instance.create {component:mainId,parent:frameId}`. Read IDs from command results. Keep instance content configurable through component properties or intentional overrides. Edit the main for changes that should propagate to all linked copies.
- For states or sizes, name the main components with explicit values such as `State=Default` and `State=Disabled`, then combine them with `component.combineVariants`. Define shared properties on the set after combining and bind each variant's corresponding layer with `component.bindProperty`. A property defined on one main beforehand belongs to that variant. Use `instance.setProperties` to set property values or switch variants on selected instances.
- Inspect the main and representative instances before changing a shared component. Preserve existing overrides, including text, visibility and component swaps. Structural edits to instance internals require editing the main or using a component slot where available.

## Styles and variables

- Use `style.create` and `style.apply` for shared paint, text, effect and grid styles. Check which style a layer uses before updating the definition because linked layers follow the change.
- Use `variable.createCollection` and `variable.create` for typed tokens. Supply values by mode name when building a theme, then use `variable.bind` with the exact supported field, such as `fill`, `gap` or `cornerRadius`. Keep semantic aliases type-compatible and resolve any ambiguity in an existing token by inspecting its collection and modes.
- Use `variable.setMode` on a page or frame when an explicit mode is part of the request. `mode: null` restores inheritance. For local library assets, inspect `library.list` and the relevant import or binding command before deciding where the reusable definition belongs.

Group related undoable edits with `batch` in its default abort mode. Use named step references for new IDs where useful. After an ambiguous failure, re-inspect the definitions and affected layers before retrying. Verify component links, property values, overrides, style links, and variable bindings with `get_node` or `inspect_document`. Screenshot representative states and check any warnings from edits or rendering.

[FigCraft MCP reference](https://github.com/storytold/figcraft/blob/v0.18.1/docs/mcp.md#conventions-agents-must-know) · [Local libraries](https://github.com/storytold/figcraft/blob/v0.18.1/docs/mcp.md#local-libraries)
