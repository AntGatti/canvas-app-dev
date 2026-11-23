---
name: canvas-validation-workflow
description: Validate Power Apps canvas app changes with Canvas Authoring diagnostics, Studio preview, accessibility checks, and save verification. Use when testing or reviewing a canvas app change.
---

# Canvas validation workflow

Use the Microsoft Canvas Apps plugin's validation workflow where available.
See [the development guide](../../../docs/canvas-development.md) for release
and accessibility checks. Keep validation within the approved app and data
scope; compilation applies changes to the coauthoring session.

1. Compare the changed controls, formulas, navigation and data bindings with
   the request and preservation requirements. Check responsive geometry and
   delegation for the actual connector and query.
2. Compile through Canvas Authoring and address introduced diagnostics. Run
   app-checker and accessibility diagnostics when available. Report existing
   issues separately; a clean compile is not proof of runtime behavior.
3. In Studio preview, exercise relevant navigation, inputs, selection,
   validation, empty and error states, reset, keyboard access and layout.
   Test live writes only with explicit authorization and approved data.
   Never assume a simulated or failed write proves persistence.
4. For a confirmed write, verify the returned identity and relevant stored
   fields. For an ambiguous response, inspect the data source before retrying.
   Test intended and restricted users where authorized.
5. Verify the app's saved state separately. Publish only with approval and
   verify the intended version. Report checks passed, failed and not run,
   including whether the app was applied, saved and published.
