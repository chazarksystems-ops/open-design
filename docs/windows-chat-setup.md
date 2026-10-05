# Connect the fork on Windows

The fork and integration branch are published at
https://github.com/chazarksystems-ops/open-design/pull/1. OpenDesign is not yet
installed on the user's PC, and its tools are not connected to the current
hosted conversation. This guide gets the existing local runtime ready for the
first real design edit. Instructions were checked on 2026-10-05; execution on
the user's Windows PC remains unverified.

## 1. Open a local chat on the PC

Use the current ChatGPT desktop app on Windows, signed into the same account
and workspace. Start a local Work or Codex chat with an approved project folder.
You can give that chat this task:

> Set up my chazarksystems-ops/open-design fork from the
> work/chatgpt-plugin-baseline branch. Follow docs/windows-chat-setup.md.
> Check prerequisites first, install the pinned dependencies, start OpenDesign
> through tools-dev, and connect its existing stdio MCP server. Verify a real
> project read, creation, revision, and preview. Keep me informed if a Windows
> installer or account setup needs my input.

The local chat needs access to that PC's shell and files. This hosted checkout
cannot install software on the PC or configure its desktop app.

## 2. Prepare the fork runtime

Use native PowerShell and a new checkout directory. The repository requires
Node 24.x and pnpm 10.33.2. Check Node and Git first:

```powershell
node --version
git --version
```

For native source builds, Python and Visual Studio C++ Build Tools with a
Windows SDK may be needed by better-sqlite3. Follow the existing
[Windows troubleshooting guide](windows-troubleshooting.md) when a
prerequisite is missing. Install the required prerequisites before dependency
installation; inspect an actual failure before changing machine settings.

From the parent folder where you want this checkout:

```powershell
git clone --depth 1 --single-branch --branch work/chatgpt-plugin-baseline https://github.com/chazarksystems-ops/open-design.git open-design-chat
Set-Location open-design-chat
npm.cmd install -g pnpm@10.33.2
pnpm.cmd --version
pnpm.cmd install --frozen-lockfile
pnpm.cmd tools-dev run web
```

The `.cmd` commands work from PowerShell without changing execution policy.
Leave the runtime terminal open and use the web URL printed by tools-dev.
Use `pnpm.cmd tools-dev stop` from this checkout to stop the managed runtime.
Avoid launching individual daemon or web processes outside tools-dev.

If dependency installation fails, preserve the first error and use the
repository's troubleshooting guide. Do not continue to MCP setup until
installation and startup succeed.

## 3. Add the local MCP connection

In a second PowerShell window, open the checkout and find the exact launch
paths:

```powershell
(Get-Command node.exe).Source
(Resolve-Path .\apps\daemon\bin\od.mjs).Path
```

In the ChatGPT desktop app, open **Settings → MCP servers → Add server**.
Name it `open-design`, choose **STDIO**, and use:

- Command: the full `node.exe` path printed above.
- Arguments: the full `od.mjs` path printed above as one argument, followed by
  `mcp` as a second argument. Keep paths containing spaces together.
- Working directory, if offered: the full checkout directory.

Save and restart the MCP connection. Use `/mcp` in the local chat to inspect
connected servers. Do not pin a daemon URL; the existing CLI discovers the
managed runtime. If the host presents different configuration fields, use
OpenDesign's **Settings → MCP server** launch snippet and match its actual
command and argument boundaries.

This directly configures the tested server. Installing the portable plugin
package is a separate optional packaging step; it does not replace the runtime.

## 4. Prove the design workflow

Ask the local chat:

> Use OpenDesign to list projects, create a throwaway project named Connection
> Test, write a simple HTML design, read it back, change one visible detail,
> and inspect its preview. Report each completed operation and its project ID.

Discover tools before calling them and use their actual schemas. MCP startup
should expose the 22 tools observed at the inspected baseline. A tool listing
alone is insufficient: the daemon must accept the project operations and the
rendered revision must be visible. Keep the test project for review.

The chat can create and revise files directly. If asking OpenDesign to launch
an agent through `start_run`, discover installed agents first. Select Codex
only when installed and authenticated; do not silently fall back to Claude.

## 5. Continue from the Android phone

When **Connections → Control this PC** is available in the desktop app, set
up the host and scan its QR code with the phone. Use the same account and
workspace and keep the PC online with the desktop app running. Remote access
availability depends on the account, workspace, and rollout.

Open a conversation on that connected host and verify the same project ID,
file revision, and preview. Phone access is complete only after those calls
work from that conversation. This existing hosted chat does not automatically
gain the PC's local MCP tools merely because the PC has been paired.

If Remote is unavailable, record the actual account limitation before selecting
an authenticated hosted MCP service or private tunnel. Do not expose the
daemon directly to the public internet as a substitute.

## References

- [OpenAI: MCP connection and desktop configuration](https://learn.chatgpt.com/docs/extend/mcp)
- [OpenAI: Windows and Android remote connections](https://learn.chatgpt.com/docs/remote-connections)
- [Fork integration scope and acceptance](chatgpt-integration.md)
- [Repository quickstart](../QUICKSTART.md)
