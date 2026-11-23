# Canvas app development guide

This guide covers local agent-assisted canvas development and the delivery
workflow. See [SharePoint CLI operations](./sharepoint-cli.md) for list
provisioning. Keep project-specific configuration in an approved internal
inventory, separate from this generic guide.

## 1. Starting point and safety

Confirm the environment, saved app, solution and data sources independently;
parameters in an app creation URL do not prove that these resources exist.
Do not assume the environment is a sandbox. Each developer needs an
organizational account, permission to edit the app and solution, access to the
selected data source, and applicable Power Apps licensing. An admin must
confirm policies and permissions.

1. Sign in, select the intended environment, and verify it is an approved
   development environment. If it is production, obtain an approved
   nonproduction environment and app before making changes.
2. Check the intended solution for an existing app before using a creation
   link. Open that app if it exists; avoid accidentally creating duplicates.
3. Otherwise, use the link to start the app, review the generated screens, save
   it in the intended environment, and confirm the app is in the intended
   unmanaged solution along with its required components. Record the saved app
   ID and the solution's **unique name** in the team's internal inventory.
4. Open the saved app for editing in Power Apps Studio. Copy its **actual edit
   URL** from the browser; an app-from-table creation link is not a substitute.
5. In Studio, enable **Settings > Updates > Coauthoring**, save as needed, and
   keep the signed-in Studio tab open while using the authoring MCP server.

Saving, enabling coauthoring, compiling, and publishing can affect a shared app.
Coordinate with other makers and follow the team's change controls.

## 2. Workstation setup

### Prerequisites

- VS Code with an agent capable of using MCP and agent plugins.
- [.NET SDK 10 or later](https://dotnet.microsoft.com/download/dotnet/10.0),
  not just the runtime. Check with `dotnet --list-sdks`; at least one version
  must start with `10.` or a later major version. Restart VS Code and terminals
  after installation. `dnx` must be available to the agent.
- Network access to the approved NuGet feed containing
  `Microsoft.PowerApps.CanvasAuthoring.McpServer`; use `dotnet nuget list source`
  if package download fails. Follow organizational proxy and package policies.
- An existing, saved canvas app with coauthoring enabled and a live Studio edit
  session, as above.

### Install the Microsoft Canvas Apps agent plugin

1. Install the .NET 10 SDK or later using the approved installer, then restart
   VS Code and verify `dotnet --list-sdks`.
2. In VS Code, open Extensions (`Ctrl+Shift+X`) and search
   `@agentPlugins canvas apps`. If prompted, select **Browse Agent Plugins**
   to open the Customizations marketplace and search `@type:plugin canvas`.
3. Select the Microsoft **Canvas Apps** plugin, review its source and install it.
   Open Chat in Agent mode and confirm the `canvas-app` and
   `configure-canvas-mcp` skills appear.
4. If agent plugins are not visible, use **Preferences: Open User Settings
   (JSON)** and add `"chat.plugins.enabled": true` inside the existing JSON
   object, without removing other settings. Reload VS Code.

For a compatible coding agent with plugin marketplace commands, the alternative
is to run these **in the agent**, not in PowerShell:

```text
/plugin marketplace add microsoft/power-platform-skills
/plugin install canvas-apps@power-platform-skills
```

In the agent, run `/configure-canvas-mcp`, supply the complete **saved app's
Studio edit URL**, and complete organizational sign-in. Ask:

```text
List the available Canvas App controls.
```

A successful response confirms the tool is reachable; also verify the app and
environment identity before approving any write. In VS Code Chat, **Configure
Tools** can show the `canvas-authoring` tools. If Studio closes or the session
expires, reopen the saved app and configure the connection again.

The plugin's setup, capabilities and troubleshooting are documented in
[Microsoft's guide](https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/create-canvas-external-tools)
and [plugin README](https://github.com/microsoft/power-platform-skills/tree/main/plugins/canvas-apps).
The Power Platform CLI (`pac`) is **optional** for this live authoring workflow;
install it separately from the [official instructions](https://learn.microsoft.com/en-us/power-platform/developer/cli/introduction)
when the team needs solution or automation operations.

### SharePoint list provisioning with CLI for Microsoft 365

Use [SharePoint CLI operations](./sharepoint-cli.md) for authentication,
permissions, site and list provisioning, and connector integration.

### Workspace agents and skills

Open the repository folder in VS Code and select **Canvas Developer** from the
agent picker for implementation, or **Canvas Reviewer** for a read-only source
review. The reviewer cannot run live diagnostics or change the app. The
workspace skills `/canvas-authoring-workflow`, `/canvas-sharepoint-workflow`
and `/canvas-validation-workflow` add focused guidance to the Microsoft Canvas
Apps plugin; they do not install or replace it. Check **Chat: Open
Customizations** for discovery, then start a new session if necessary.

## 3. Development loop

1. Confirm the correct app, environment and solution. Coordinate edits with
   other makers and establish a recoverable baseline before a substantial change.
2. In Studio's **Data** pane, confirm the intended Dataverse table or SharePoint
   list is connected. Use `/add-data-source` for guided setup if needed. Inspect
   the actual schema, column types, required fields, relationships and user
   privileges before implementing formulas. Do not infer the schema from a URL.
3. Run `/canvas-app` or ask the agent to edit the existing **Canvas App**.
   For example:

   ```text
   Inspect the existing Canvas App and its connected Dataverse table.
   Report screens, data sources, schema and any validation issues.
   Do not change the app yet.
   ```

4. Define a small change, acceptance criteria and permitted data access. Ask the
   agent to synchronize the existing app, propose changes, validate them through
   the authoring server and report errors. Review the generated `.pa.yaml`
   screens, controls and Power Fx before accepting changes.
5. Test in Studio's preview with representative, non-sensitive test data. Save
   the app, then publish only after review and the team's approval. Saving and
   live synchronization do not by themselves mean the published app is updated.
6. Capture the change in the team's solution/source-control process and repeat
   in small increments.

**Important:** `sync_canvas` downloads the live app state and overwrites
matching local YAML files; preserve or commit local work first.
`compile_canvas` validates **and applies** local YAML to the live authoring
session; it is not a read-only lint command. Do not approve it against the wrong
app or assume it publishes the app. A successful compile is not a functional,
security or accessibility test.

### Power Fx and data-source practices

- Use the actual Studio data-source and column names; display names and logical
  names are not interchangeable. Build and test forms against the real schema.
- Handle validation, empty/loading states, and failed saves explicitly. Check
  required fields and permissions; do not report success when a write fails.
- Check [delegation](https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/delegation-overview)
  for each data source, operator and column. Resolve delegation warnings and
  test with more rows than the configured nondelegable data-row limit; otherwise
  filters or searches may return incomplete results. Raising the limit is not a
  substitute for delegation.
- Use responsive containers and meaningful accessible labels; test keyboard
  navigation, focus order, contrast, screen readers and mobile layouts.
- Dataverse roles or SharePoint permissions, as applicable, enforce data
  security. Hiding a control or filtering a gallery is not authorization.
  Test with representative roles.
- Add only approved connectors and connections. Respect environment data
  policies, least privilege and licensing. Never put passwords, tokens,
  connection strings or sensitive records into prompts, logs or source control.

## 4. September 2026 update: what matters here

Source: [What's new in Power Platform: September 2026 feature update](https://www.microsoft.com/en-us/power-platform/blog/power-apps/whats-new-in-power-platform-september-2026-feature-update/).
Availability may vary by tenant, region and release settings; verify in Studio.

### Canvas authoring agent: generally available

The update announces GA for the Canvas Apps authoring agent plugin and its MCP
integration. It complements Studio: the agent can inspect controls, connectors
and data-source schemas; generate and modify screens and Power Fx; validate
YAML; and synchronize changes with the active coauthoring session. Developers
remain responsible for review, testing, security and publishing.

### Fluent screen templates and modern controls

- Six additional Fluent 2 screen templates supplement three recently shipped
  Fluent templates. They replace older out-of-box classic-control templates:
  use the new-screen experience, choose a suitable responsive starting point,
  connect data and adapt it to the app.
- The control-update surface can update eligible controls in bulk **on the
  current screen**, enable required modern-control settings, and supports
  Studio undo/redo. Before updating, review control-specific changes; afterward
  fix renamed properties, enum values and behavior changes, and retest formulas,
  input events, reset behavior and accessibility. Do not mass-upgrade an
  existing app without a baseline and review.
- The update describes a modern progress bar with determinate/indeterminate
  modes, Power Fx values, semantic colors, theme-aware styling and accessibility
  semantics. Modern avatar and spinner controls are also available. Date picker
  interaction/reset fixes and formula preservation for checkbox, rating and
  toggle are noted. Inspect actual control properties and
  [current update guidance](https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/controls/modern-controls/modern-control-updates)
  rather than assuming classic and modern controls behave identically.

### Other announcements, not canvas app prerequisites

- **Model-driven apps:** refreshed header/navigation is GA and opt-in;
  display density is preview and requires that refresh. Model-driven app-builder
  skill is GA. Generative pages can be embedded in forms with `recordId`;
  connector-backed generative pages are preview. These are separate from the
  canvas app workflow and preview features require separate governance review.
- **Power Automate:** home-page quick-start cards and server-side search in
  My Flows improve discovery; neither is needed to configure canvas MCP.
- **Learning:** Power Series offers hands-on labs; the update also links to ALM,
  architecture and maker training. See the [Power Series labs](https://aka.ms/PowerSeries/Labs)
  and [Power Platform ALM learning path](https://learn.microsoft.com/training/paths/application-lifecycle-management/).

## 5. Review, test and release

Use an unmanaged solution in development, review changes in source control, and
promote a managed solution through approved test and production environments
using the organization's Power Platform pipeline or CI/CD process. Verify
dependencies, connection references, environment variables, target permissions
and licensing for each environment. Use the actual solution unique name, not
the solution GUID, where a deployment tool asks for a unique name. Agree on a
rollback procedure before deployment. See [Power Platform ALM](https://learn.microsoft.com/en-us/power-platform/alm/overview-alm).

Minimum acceptance checklist:

- [ ] Correct environment, saved app, solution and data source confirmed; baseline
      available and edits reviewed by another developer.
- [ ] Studio/App checker and MCP validation issues addressed; delegation
      warnings assessed and large-data searches verified.
- [ ] Browse, search, create, edit and delete tested where supported and
      authorized, including required fields, invalid inputs, empty results and
      failed writes. Use only approved test records.
- [ ] App sharing and the selected data source's access tested with intended
      and restricted users; connector and DLP policies checked.
- [ ] Keyboard, screen reader, contrast, responsive layout and relevant device
      sizes tested.
- [ ] Save/publish and solution deployment verified in the target environment;
      connection references and environment-specific configuration checked.

## 6. Troubleshooting

| Symptom | Action |
| --- | --- |
| Plugin missing | Check Agent mode and plugin settings, search for the Microsoft Canvas Apps plugin, reload VS Code, and check organizational restrictions. |
| `dotnet`/`dnx` unavailable or MCP package fails | Install the SDK, restart VS Code, verify `dotnet --list-sdks` and `dotnet nuget list source`; ask the administrator for an approved package feed if needed. |
| Connection fails or expires | Open the saved app's Studio **edit** URL, enable coauthoring, keep the tab open and signed in, then rerun `/configure-canvas-mcp`. Confirm account, environment and edit permissions. |
| Data source missing | Add it through Studio's Data pane, verify permissions and DLP policy, then ask the agent to list sources again. |
| Changes absent or errors reported | Check the selected app and active session; inspect validation/compile errors before retrying. Reconnect if the Studio session expired. |
| Local YAML unexpectedly changed | `sync_canvas` overwrites matching files. Recover from the team's saved baseline or source control and coordinate before syncing again. |

For plugin issues use [Microsoft's Canvas Apps issue tracker](https://aka.ms/power-skills-canvas-issues);
include tool versions and sanitized error details, never credentials.
