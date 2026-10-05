# OpenDesign fork: chat design workspace

## Goal

Use our own OpenDesign fork as a reusable workspace for creating, inspecting,
and refining designs with ChatGPT and Codex, reducing repeated transfers to
Claude Design. Preserve editable files, visual previews, and project history.
The workspace should support interface prototypes, websites, presentations,
and visual design exploration. Silo is an initial use case, not the only one.

## Inspected baseline

- Upstream: https://github.com/nexu-io/open-design
- Inspected commit: `53231d40b778d88eba23f35547bf99485d3ae9fc`
- Inspection date: 2026-10-05
- Root package version: `0.23.1`
- Runtime requirements: use the root `package.json` and `AGENTS.md`.
- License: Apache-2.0; retain upstream license and applicable notices.
- Requested fork destination: `chazarksystems-ops/open-design`.
- Customization branch: `work/chatgpt-plugin-baseline`.

Fork creation and account connection must be verified independently. A local
checkout, package archive, or successful sign-in submission is not evidence
that either is complete.

## Existing implementation to reuse

`apps/daemon/src/mcp.ts` implements the stdio MCP server. The `od mcp`
subcommand is implemented in `apps/daemon/src/cli.ts` and proxies actions to
the daemon's existing HTTP endpoints. Reuse these contracts instead of
implementing another project database, artifact writer, or agent runner.

| Workflow | Existing tools observed in source |
| --- | --- |
| Find a workspace | `list_projects`, `get_active_context`, `get_project` |
| Inspect a design | `get_artifact`, `get_file`, `list_files`, `search_files` |
| Create and revise | `create_project`, `create_artifact`, `write_file` |
| Commission generation | `list_agents`, `list_skills`, `list_plugins`, `start_run`, `get_run` |
| Gather a brief | `collect_brief`, `confirm_brief` |

Exports are an existing daemon/UI/CLI capability. The inspected MCP tool list
does not contain a general export tool. Inspect the export contracts and add
an adapter only if the chat workflow needs it; do not advertise an implemented
MCP export operation before it exists.

Native engineering CAD, PCB layout, and electrical validation are separate
future adapters. A rendered concept is not a manufacturable hardware design.

## First implementation

The existing `plugins/open-design/` integration now has a portable
`plugin.json` and `mcp.json`. Its compatibility manifest and MCP config use
the same identity, version, and server command. Removing the fixed daemon URL
allows the upstream CLI's existing runtime discovery to select the live
daemon, including after a restart.

This first change adds packaging and connection documentation. It does not
replace the existing UI, alter the generation pipeline, deploy a remote
server, install a plugin into an account, or configure the user's computer.

## Connection choices

| Where the conversation runs | Connection | Completion evidence |
| --- | --- | --- |
| Supported local Codex or desktop host | The fork's `od mcp` stdio server | Host discovers tools and successfully reads a test project |
| Phone using a supported connected computer session | The computer provides OpenDesign and its configured MCP server | Phone conversation reads and revises the same test project |
| Hosted ChatGPT through a private connection | A supported private MCP connection, such as Secure MCP Tunnel | Actual chat tool discovery and a successful project call |
| Future shared hosted service | Authenticated remote MCP endpoint backed by an explicitly selected workspace | Auth, project access, and preview behavior verified in the target host |

Relevant current documentation:

- https://learn.chatgpt.com/docs/extend/mcp
- https://learn.chatgpt.com/docs/remote-connections
- https://developers.openai.com/api/docs/guides/secure-mcp-tunnels

Do not migrate the desktop application into a different hosting architecture
merely to package a plugin. Use the local runtime first and select the final
remote connection based on the actual account, workspace, and computer setup.

## First usable workflow

1. Select or create an OpenDesign project.
2. Read its brief, artifact, and relevant source files.
3. Create a design or propose a concrete revision.
4. Apply the authorized revision through the existing project tools.
5. Render and inspect the actual output before calling it complete.
6. Return the artifact, preview, and a short description of what changed.

For two competing designs, keep both variants and record their tradeoffs in
project files. For Silo, compare functionality, durability, and aesthetics,
with cost and serviceability included when they affect the choice.

Generation through `start_run` spawns an agent in OpenDesign. Discover installed
agents before choosing one. Selecting Codex should not silently fall back to
Claude. Runtime availability, authentication, and provider billing are checked
at the target host; this package does not promise a free generation service.

## Acceptance checks

- Preserve the GitHub fork relationship and verify its default branch.
- Parse the portable and compatibility manifests and verify synchronized
  identity/version/server command.
- Verify MCP startup and real tool discovery against this fork's built CLI.
- Verify a harmless read, then creation and revision of a throwaway design
  project against the real daemon, with readable error reporting.
- Verify that the chosen host can actually display or retrieve a preview.
  A localhost preview URL alone is insufficient for a phone conversation.
- Verify the plugin or MCP connection in the actual user-facing chat host.
- Run repository guard and type checks as required by `AGENTS.md`; document
  baseline failures and environment blockers instead of claiming they passed.

Only after these checks should the tool be described as connected and usable
from hosted chat. Package validation and tests with a mocked daemon are useful
intermediate checks, not that final acceptance.

## Subsequent work

1. Complete the user-host connection and prove the first workflow end to end.
2. Add chat export access if the existing tools leave an observed gap.
3. Add a design-review workflow with visible variants, comments, and decisions.
4. Add preview/selection context in chat where it improves iteration.
5. Add Silo-specific engineering adapters after the shared design workflow is
   working.

Keep upstream `main` as the synchronization baseline and review customization
on a separate branch. Preserve provenance; avoid broad rebranding, unrelated
refactors, or release automation changes in the first integration change.
