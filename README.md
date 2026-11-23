# AI-assisted Canvas app development

This repository is a starting point for developing Microsoft Power Apps canvas
apps with AI, specifically GitHub Copilot in VS Code and Microsoft's Canvas
Apps plugin. It provides technical guides, reusable agents and skills for
live authoring, SharePoint integration, validation and release.

**Idea Lab is an example**, not a prerequisite or a generic app template. The
same workflow can be applied to another app after verifying its environment,
connections, schema and permissions.

## Get started

1. Follow the [canvas development guide](docs/canvas-development.md) to set up
   VS Code, the Canvas Apps plugin, Canvas Authoring MCP and coauthoring.
2. Copy [config.example.json](config.example.json) to `config.local.json` and
   fill in the values for your approved environment. This is a manual
   reference file, not configuration automatically consumed by the tools.
   Never store credentials, access tokens or secrets in either file.
3. Open the saved app in Studio, confirm its identity and permissions, then
   connect Canvas Authoring to the active coauthoring session. Preserve local
   edits before synchronization.
4. In Copilot Chat, choose **Canvas Developer** to implement a change, or
   **Canvas Reviewer** for a read-only source review. The workspace skills
   cover authoring, SharePoint and validation; see the development guide for
   discovery and prerequisites. Review all AI-generated changes.
5. Test with authorized data, verify saving separately from compilation, and
   publish only through your approved release process.

## Repository contents

- [Canvas development guide](docs/canvas-development.md): setup, authoring,
  testing and release.
- [SharePoint CLI guide](docs/sharepoint-cli.md): authentication, permissions,
  provisioning and schema inspection.
- [Agents](.github/agents/): implementation and read-only review roles.
- [Skills](.github/skills/): focused workflows that complement the Microsoft
  Canvas Apps plugin.
- [Configuration template](config.example.json): environment values to keep
  in the ignored local configuration.

The local `idea-lab/` app source and `project/` notes are excluded from Git
alongside `config.local.json`. They are not delivered by a repository clone.
Use an approved private location for project-specific files and verify
organizational policy before sharing app source or environment details.
