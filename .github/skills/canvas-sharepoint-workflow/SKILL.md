---
name: canvas-sharepoint-workflow
description: Connect a canvas app to SharePoint and inspect or provision lists with CLI for Microsoft 365. Use for SharePoint schema, delegated consent, list access, choices, or Power Apps data connections.
---

# Canvas and SharePoint workflow

Use [the SharePoint CLI guide](../../../docs/sharepoint-cli.md) for commands
and consent checks. The CLI for Microsoft 365 (`m365`) is separate from the
Power Platform CLI (`pac`) and the Power Apps runtime connector.

1. Confirm the intended tenant, site, list, environment and authorization
   without copying tokens, account status or internal identifiers into public
   files. Check the signed-in identity, app consent and the user's site
   permissions independently.
2. Inspect the site and existing list before proposing changes. Authentication
   or authorization failure does not establish that a resource is absent.
   Review requested site, list, column, permission or record changes before
   performing them; obtain approval when required.
3. Add a connection through Studio's Data panel when necessary. Use the
   Microsoft `add-data-source` skill if available, then verify the source
   and schema with Canvas Authoring. Do not assume CLI access means the
   canvas app connection exists.
4. Use actual connector field names and types. A SharePoint single-choice
   field uses a record with `Value`; a multi-choice field uses a table of
   such records. Check required fields and limits against the actual schema.
5. Validate writes, permissions, errors and delegation using approved test
   data. Show success only after confirmed writes; preserve drafts on
   failure and warn that an ambiguous result may require checking the list
   before retrying. Never create or delete test records without authorization.
