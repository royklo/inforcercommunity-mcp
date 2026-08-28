# Which assistants work

[← README](../README.md)

This server runs on your own machine, started by your assistant. So any assistant that can start a
local tool will work. Cloud-only assistants cannot, because there is nothing on their side to start
it. [Why it works this way →](SECURITY.md#why-it-runs-on-your-machine)

## Where each assistant keeps its settings

| Assistant | Settings file |
|---|---|
| Claude Code | `~/.claude.json` - or use `claude mcp add` |
| Claude Desktop (macOS) | `~/Library/Application Support/Claude/claude_desktop_config.json` |
| Claude Desktop (Windows) | `%APPDATA%\Claude\claude_desktop_config.json` |
| Cursor | `~/.cursor/mcp.json` |
| Windsurf | `~/.codeium/windsurf/mcp_config.json` |
| VS Code (Copilot) | `.vscode/mcp.json` - [different format](#vs-code) |
| Codex | `~/.codex/config.toml` - [different format](#codex) |

All of these except VS Code and Codex take the same block as
[the README](../README.md#set-it-up). Paths verified against each vendor's own documentation; see
[Sources](#sources).

Other assistants that support local tools will most likely work too, using the same block. They are
left off this list only because their settings location has not been checked.

## On Windows

On Windows, `npx` is a batch file rather than a program. Several assistants start it in a way that
cannot run a batch file, so the server never appears and the log says `spawn npx ENOENT`. Putting
`cmd /c` in front fixes it, and does no harm on assistants that would have worked anyway.

Use this instead of the block in the README:

```json
{
  "mcpServers": {
    "inforcer": {
      "command": "cmd",
      "args": ["/c", "npx", "-y", "inforcercommunity-mcp@latest"],
      "env": {
        "INFORCER_API_KEY": "your-api-key",
        "INFORCER_REGION": "uk"
      }
    }
  }
}
```

The change is the same everywhere: `command` becomes `cmd`, and `/c`, `npx` go in front of the
arguments. VS Code still uses `servers` instead of `mcpServers`, and Codex still uses TOML.

For Claude Code, on one line:

```bash
claude mcp add inforcer -s user -e INFORCER_API_KEY=your-api-key -e INFORCER_REGION=uk -- cmd /c npx -y inforcercommunity-mcp@latest
```

macOS and Linux need none of this.

## VS Code

One click from [the README](../README.md#set-it-up). VS Code asks for your API key, then your
region. The key goes into VS Code's own secret storage - not into a settings file, and not into the
link, so it never reaches your browser history either.

To do it by hand instead: VS Code uses `servers`, not `mcpServers`. That is the mistake most likely to cost you an afternoon.

```json
{
  "servers": {
    "inforcer": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "inforcercommunity-mcp@latest"],
      "env": {
        "INFORCER_API_KEY": "your-api-key",
        "INFORCER_REGION": "uk"
      }
    }
  }
}
```

Put it in `.vscode/mcp.json` for one project, or run **MCP: Open User Configuration** from the
Command Palette for all projects.

The tools only appear in **Agent** mode, not ordinary Copilot Chat.

## Codex

Codex uses TOML. Settings go in their own `env` table rather than inline.

```toml
[mcp_servers.inforcer]
command = "npx"
args = ["-y", "inforcercommunity-mcp@latest"]

[mcp_servers.inforcer.env]
INFORCER_API_KEY = "your-api-key"
INFORCER_REGION = "uk"
```

Or let Codex write it for you:

```bash
codex mcp add inforcer --env INFORCER_API_KEY=your-api-key --env INFORCER_REGION=uk \
  -- npx -y inforcercommunity-mcp@latest
```

Run `/mcp` inside Codex to confirm it loaded.

## Assistants that can't use this

| Assistant | Why |
|---|---|
| Microsoft 365 Copilot | Runs in Microsoft's cloud. It can only reach tools published on the internet. |
| Copilot Studio | Same. It connects to remote tools over HTTP, not to anything on your machine. |
| ChatGPT connectors | Same. |

This is not a gap in those products, and not one in this server either. They are built to call tools
over the internet; this one is built to stay on your machine, so your Inforcer key never leaves it.
[The reasoning →](SECURITY.md#why-it-runs-on-your-machine)

If you need Inforcer data inside Microsoft 365 Copilot, produce it with the
[PowerShell module](https://github.com/royklo/InforcerCommunity) and save the file somewhere Copilot
can read.

## Claude Code: `-s user`

The README uses `-s user`, which makes the server available in all your projects. Without it, it
only works in the folder you ran the command in.

There is also `-s project`, which saves the settings into a `.mcp.json` file inside your project so
your team gets them too.

> **Don't use `-s project` while your API key is in the command.** That file is meant to be committed
> to version control, so your key would be committed with it. If you want a shared team setup, commit
> a `.mcp.json` that refers to `${INFORCER_API_KEY}` and let each person set that themselves.

## A note on versions

The examples use `@latest`, so your assistant picks up fixes and new endpoints on its own the next
time it starts. This is the recommended setup, and it is why the examples do not name a version.

Updates arrive when the server starts, not while it is running. If you have had the same assistant
session open for days, restart it to pick up the newest version.

If you would rather approve each update yourself, replace it with an exact version, for example
`inforcercommunity-mcp@1.4.2`. Your assistant will then stay on that version until you change it.
That also means a future version that changes how something works cannot arrive unannounced.

## Sources

- [Claude Desktop config location, modelcontextprotocol.io](https://modelcontextprotocol.io/docs/develop/connect-local-servers)
- [MCP servers in VS Code](https://code.visualstudio.com/docs/copilot/customization/mcp-servers)
- [VS Code install-link scheme, MCP developer guide](https://code.visualstudio.com/api/extension-guides/ai/mcp).
  The button goes through `insiders.vscode.dev/redirect`, not the `vscode:` scheme directly, because
  GitHub's markdown sanitiser strips non-standard schemes - the same pattern
  [github/github-mcp-server](https://github.com/github/github-mcp-server) uses.
- [MCP in Codex](https://learn.chatgpt.com/docs/extend/mcp?surface=cli)
- [MCP in Cursor](https://cursor.com/docs/mcp)
- [Cascade MCP integration, Windsurf](https://docs.windsurf.com/windsurf/cascade/mcp)
- [MCP in Copilot Studio](https://www.developerscantina.com/p/mcp-copilot-studio-streamable-http/)
- Windows `cmd /c`: [modelcontextprotocol/servers#3460](https://github.com/modelcontextprotocol/servers/issues/3460)
  and [anthropics/claude-code#58510](https://github.com/anthropics/claude-code/issues/58510). The
  official setup guide still shows bare `npx` for Windows, which is why this page says otherwise.
