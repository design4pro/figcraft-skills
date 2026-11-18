---
name: figcraft-layout
description: Build or edit screens and layer hierarchies in FigCraft. Use for frames, typography, layout, alignment, resizing, or visual fixes on the canvas.
---

# FigCraft layout

## Find the canvas

- Use the FigCraft server chosen for the task. Default to `figcraft-headless` for file-based work, accept a configured alternative such as `figcraft`, and use an attached server such as `figcraft-desktop` for the open editor. Keep document and layer IDs on the server that returned them. If the intended connection is unavailable, identify the missing connection before editing.
- Open an explicit input path with `open_file`, or locate a named open document with `doc.list` via `run_command`. Resolve any ambiguity or `opened.stale` before editing. Pass the resolved `document` and explicit layer IDs wherever the tool supports them.
- Read `inspect_document` with a shallow depth and `outline: true`, then use `get_node` on target frames for geometry, layout, and children. If the result is truncated, inspect smaller subtrees.

## Build or edit

1. For new screens, create top-level frames at the requested sizes and build named children inside them. For edits, read the existing frame hierarchy, sizing, styles, and variables and reuse the established structure where it fits the request.
2. Use `create_node` for layers and `set_properties` for targeted updates. Use `list_commands` to confirm command parameters for operations such as reparenting or reordering. `node.reparent` takes `ids` and `parent`, and `node.reorder` takes `id` and `index`. Within auto layout, child order controls flow order.
3. Group related edits in a `batch` with `onError: "abort"` so they form one undo step. A creation step named with `as` can pass its returned ID to a later step via `{"$ref":"step-name"}`. After a lost reply, inspect the affected subtree before retrying.

### Geometry and auto layout

- Coordinates are page units with y increasing downward. The `x` and `y` of a layer are relative to the containing frame or the page. Groups add no coordinate space. Use `absoluteBounds` when comparing layers under different parents.
- Set the frame's `layout`, `gap`, `padding`, and alignment to arrange flow children. Use `sizingH` and `sizingV` for `fixed`, `hug`, or `fill` sizing. Inspect both parent and child after changing sizing. Setting `fill` on a child can switch a hugging parent to fixed sizing on that axis.
- Use `absolute: true` and constraints for a child that intentionally sits outside the flow, such as an overlay. For text that should wrap to the available width, give it a fixed or fill width under the intended parent and allow its height to grow.

## Verify the result

Read each changed frame with `get_node` to check parentage, child order, dimensions, and sizing. Render it with `screenshot` scoped to the frame ID. Check for text clipping or unexpected wraps, alignment and spacing errors, and content outside the frame. Compare against any supplied reference and inspect requested viewport variants individually.

For wrapped and grid layouts, consult the [FigCraft MCP layout conventions](https://github.com/storytold/figcraft/blob/v0.18.1/docs/mcp.md#conventions-agents-must-know).
