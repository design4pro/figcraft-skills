---
name: figcraft-prototype
description: Build, edit, or verify FigCraft prototypes with flow start points, navigation, overlays, component interactions, transitions, and variable-driven conditions.
---

# FigCraft prototypes

Use the FigCraft MCP backend that holds the target document. `figcraft-headless` is the default for local files and new designs. Use the configured desktop backend, such as `figcraft-desktop`, when the user directs work to an attached document. Open an explicit input path with `open_file`, or locate a named open document with `doc.list` via `run_command`. Resolve any ambiguity or `opened.stale` before editing. Pass the resolved `document` on tools that support it, then inspect the relevant page and layers for frame and hotspot IDs.

## Wire the flow

1. Read the page's `flows` and source layers' `interactions`, using `inspect_document` and focused `get_node` calls. Identify the start frame, hotspots, destinations, and return paths. Use `list_commands` with a filter such as `proto.interaction.add` to confirm the installed parameters.
2. Select the target page with `page.select` if needed. Use `proto.flow.add {start:frameId,name}` on a top-level frame on that page. Use `proto.flow.rename` to rename an existing flow. Add a click-through with `proto.interaction.add {id:hotspotId,dest:frameId}`. Use explicit trigger and action objects for overlays, `changeTo`, `back`, or other behaviors. Configure a transition only when it helps communicate the interaction or the user requests it.
3. Use `proto.interaction.set` to edit an existing interaction by its current index. Read the layer again after removing or reordering interactions before using indices in a later call. Use `proto.evaluate` with the source layer ID to check expressions against variables in that layer's modes. For component variants, inspect the main and the relevant instance, since an instance can override the main variant's interaction for the same trigger.

Group related undoable commands with `batch` in its default abort mode. A lost response from the attached backend can follow a successful edit. Inspect the flow and source layers before retrying so you avoid duplicate interactions.

## Verify the prototype

- In either backend, inspect the page for the expected flow start and verify each changed interaction's trigger, action, destination and transition on the source layer. Check that the destination layers exist and that the requested forward, return and overlay-dismissal paths are wired. Use scoped screenshots to verify rendered screens.
- In an attached session, focus the target window and bring the document tab forward with `doc.switch` if needed. Start `proto.preview` or `proto.present` with the flow's `start` frame ID. Use window input to exercise the player and check `inspect_ui` for `preview` or `presenter` state. The MCP `pointer` tool targets the design canvas. Report which paths were exercised and which still need a player check. In headless mode, report the structural, expression, and render checks that were performed.

[FigCraft MCP prototype reference](https://github.com/storytold/figcraft/blob/v0.18.1/docs/mcp.md#prototypes) · [FigCraft prototype workflows](https://craft.wiki/apps/figcraft/workflows/#make-a-prototype)
