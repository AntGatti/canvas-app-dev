---
name: canvas-authoring-workflow
description: Safely develop or modify a Power Apps canvas app with the Canvas Apps plugin and live coauthoring. Use for canvas screens, controls, Power Fx, app synchronization, or Canvas Authoring MCP.
---

# Canvas authoring workflow

Use the Microsoft Canvas Apps plugin's `canvas-app` skill for its current
create/edit routing and validation procedure. This skill adds operational
checks; it does not replace the plugin. If the plugin or authoring tools are
missing, follow [the development guide](../../../docs/canvas-development.md)
and report the setup blocker rather than fabricating results.

1. Verify the saved app, environment, solution, coauthoring session and
   permissions. Coordinate with other makers; an app creation URL does not
   establish the identity of a saved app.
2. Select a dedicated directory for that app's `.pa.yaml` files. Preserve any
   uncommitted local changes before `sync_canvas`, which overwrites matching
   files. Synchronize before inspecting or editing current app state.
3. Inspect the affected screens and discover exact control variants,
   properties, data sources and field types through authoring metadata. Do
   not infer identities or data schemas from names or screenshots.
4. Make the smallest change that satisfies the requested behavior. Preserve
   existing names, hierarchy, bindings, feedback and responsive layout
   unless the change requires otherwise. Add concise Power Fx comments where
   logic is not obvious.
5. Follow the plugin's compilation and validation workflow. `compile_canvas`
   applies YAML to the live coauthoring session; it does not prove that the
   app was saved, published, or functionally tested.
6. Verify applicable behavior in Studio with authorized data, save when
   appropriate, and verify the saved state. Publish only when requested and
   approved. Report unverified behavior and blockers explicitly.
