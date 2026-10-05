# OpenDesign chat integration

This folder packages the fork's existing MCP server for supported agent hosts.
`plugin.json` and `mcp.json` use the portable Agent Plugins format. The existing
`.claude-plugin/plugin.json` and `.mcp.json` remain compatibility files.
This is an external-agent integration package, not an OpenDesign-native
scenario plugin with `open-design.json`.

## Local prerequisites

Build this fork using the repository's documented setup. Put this fork's
`od` executable on the agent host's PATH and confirm that `od mcp --help`
resolves to that installation. Start OpenDesign through its supported lifecycle
or packaged application. Follow the root [AGENTS.md](../../AGENTS.md) for
runtime and daemon data directory rules.

Install this folder with the target host's supported plugin installation flow.
The server command is `od mcp`. There is no hard-coded daemon port: the CLI
uses its existing runtime discovery. An explicit `OD_DAEMON_URL` remains
available when selecting a particular running daemon is necessary.

## What this package provides

- Project and file discovery through the existing MCP tools.
- Reading a design artifact and its related files.
- Creating artifacts and revising project files.
- Existing design-agent discovery, generation, and run-status tools.

The server also exposes existing deletion and sign-in tools. This package does
not add a permission boundary or limit the upstream tool set. Tool availability
does not authorize an action; the host's permissions and the user's request
still govern it.

## Hosted chat and phone access

A local stdio command cannot run directly on a phone or in a hosted chat merely
because this package is uploaded. Use a supported connection to the computer
running OpenDesign, or configure a private MCP connection such as Secure MCP
Tunnel where the account and workspace support it. Keep the OpenDesign daemon
local; connect its MCP server rather than exposing the full daemon API.

See the [integration plan](../../docs/chatgpt-integration.md) for acceptance
criteria, connection choices, and remaining work. This source package is not
proof of an installed or connected ChatGPT tool.
