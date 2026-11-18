---
name: figcraft-documents
description: Create, open, inspect, and save FigCraft design documents. Use for file setup, page and layer inventories, importing existing designs, reloading a file changed on disk, and troubleshooting FigCraft MCP connections.
---

# Manage FigCraft documents

## Find the document

- Use `figcraft-headless` for ordinary file work and a user-configured server such as `figcraft-desktop` when the request targets an open desktop window. An existing local server such as `figcraft` can be used when that is the intended connection. Keep the same server for related calls since document IDs are local to its session.
- Run `doc.list` via `run_command` for open document IDs, names, and state. If the request provides a file path, use `open_file` with that absolute path. An explicitly named document takes precedence over the active tab. Resolve ambiguous matches before editing.
- Use `list_commands` to check command parameters on the connected version. Create a new document with `file.new`, or use `file.newFromTemplate` if the user wants a template. Headless MCP starts with an untitled document that can be used if it is empty and intended for the task.
- If no FigCraft MCP is available, check whether the plugin is enabled and the host can launch `figcraft-cli`. Give the relevant setup step from the [FigCraft agent guide](https://craft.wiki/apps/figcraft/ai-agents/).

## Inspect, import, and save

- Keep `document.id` from `open_file` or the top-level `id` from `inspect_document`. Pass it as `document` on subsequent calls that support it. Begin with a shallow `inspect_document` outline, using `allPages` for an inventory across pages. Drill into individual nodes with `get_node` and narrow `fields` or page through children if the response reports truncation.
- Read `opened.warnings` and `opened.repairs` after importing and report material changes or omissions. Prefer saving imported designs to a `.figcraft` copy. Treat `.fig` output as an experimental export that may omit features and leaves the original document's save state intact.
- If `open_file` reports `opened.stale`, inspect the tab state and determine which version the task should use. Use `reload: true` when the disk version is wanted, the tab has no unsaved changes, and the document is outside a live session. Reload replaces the document state, so use the returned ID. To preserve both versions, save edits to a new `.figcraft` path and open the original path again. Saving to a new path retargets the edited tab.
- For an in-place save, call `save_file` with `document` and omit `path` so FigCraft checks for changes on disk. Pass an unused absolute `.figcraft` path for the first save or to preserve a copy. If the in-place save detects outside edits, resolve the conflict before saving again. Check the reported output and document state, and report the saved path and any unresolved warnings.
- Group related undoable edits with `batch` and prefer its default abort behavior. After a lost response, inspect the document and file state before retrying a save or edit. If file access is refused, report the path or automation root that needs attention.
