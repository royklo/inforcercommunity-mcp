# InforcerCommunity MCP

Ask your AI assistant questions about your [Inforcer](https://inforcer.com) tenants, in plain
English, and get real answers back.

> *"Which of my tenants are furthest from their baseline?"*
> *"In Contoso, which policy controls password expiry?"*
> *"Who was given Global Administrator last month?"*

Your assistant normally can't see your Inforcer data. This connects the two.

> **Community project. Not owned, endorsed, or maintained by Inforcer.**
> Built by [Roy Klooster](https://github.com/royklo). Bugs here are mine, not theirs.

## What you need

- **Your Inforcer API key**, from the Inforcer portal.
- **Your region**: `uk`, `eu`, `us`, or `anz`. Ask whoever gave you the key.
- **Node.js 20 or newer.** Not sure? Ask your assistant: *"do I have Node.js 20 or newer?"*

Nothing to download. Your assistant fetches this automatically.

## Set it up

**VS Code** - one click, then [add your key](docs/CLIENTS.md#vs-code):

| Platform | VS Code | VS Code Insiders |
|---|---|---|
| macOS & Linux | [![Install in VS Code](https://img.shields.io/badge/VS_Code-Install-0098FF?style=flat-square&logo=visualstudiocode&logoColor=ffffff)](https://insiders.vscode.dev/redirect/mcp/install?name=inforcer&config=%7B%22type%22%3A%22stdio%22%2C%22command%22%3A%22npx%22%2C%22args%22%3A%5B%22-y%22%2C%22inforcercommunity-mcp%40latest%22%5D%7D) | [![Install in VS Code Insiders](https://img.shields.io/badge/VS_Code_Insiders-Install-24bfa5?style=flat-square&logo=visualstudiocode&logoColor=ffffff)](https://insiders.vscode.dev/redirect/mcp/install?name=inforcer&config=%7B%22type%22%3A%22stdio%22%2C%22command%22%3A%22npx%22%2C%22args%22%3A%5B%22-y%22%2C%22inforcercommunity-mcp%40latest%22%5D%7D&quality=insiders) |
| Windows | [![Install in VS Code](https://img.shields.io/badge/VS_Code-Install-0098FF?style=flat-square&logo=visualstudiocode&logoColor=ffffff)](https://insiders.vscode.dev/redirect/mcp/install?name=inforcer&config=%7B%22type%22%3A%22stdio%22%2C%22command%22%3A%22cmd%22%2C%22args%22%3A%5B%22%2Fc%22%2C%22npx%22%2C%22-y%22%2C%22inforcercommunity-mcp%40latest%22%5D%7D) | [![Install in VS Code Insiders](https://img.shields.io/badge/VS_Code_Insiders-Install-24bfa5?style=flat-square&logo=visualstudiocode&logoColor=ffffff)](https://insiders.vscode.dev/redirect/mcp/install?name=inforcer&config=%7B%22type%22%3A%22stdio%22%2C%22command%22%3A%22cmd%22%2C%22args%22%3A%5B%22%2Fc%22%2C%22npx%22%2C%22-y%22%2C%22inforcercommunity-mcp%40latest%22%5D%7D&quality=insiders) |

**Claude Code** - paste into a terminal, using your own key and region:

```bash
claude mcp add inforcer -s user \
  -e INFORCER_API_KEY=your-api-key \
  -e INFORCER_REGION=uk \
  -- npx -y inforcercommunity-mcp@latest
```

**Claude Desktop, Cursor, Windsurf** - paste into your assistant's settings file:

```json
{
  "mcpServers": {
    "inforcer": {
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

**On Windows, using Codex, or can't find the settings file?**
[Which assistants work](docs/CLIENTS.md) covers all of it.

Then **restart your assistant**. It only reads its settings when it starts.

## Check it worked

Ask your assistant to **run `inforcer_status`**. If you see `"state": "ok"`, you're done.

Not working? The usual cause is the wrong **region**, which fails just like a wrong key.
[Other causes and fixes](docs/USAGE.md#if-something-isnt-working).

## Try it

> *"List my Inforcer tenants with their alignment scores."*

Use tenant names like "Contoso", not ID numbers. You never have to learn a command. Just ask.

## Documentation

| | |
|---|---|
| **[Which assistants work](docs/CLIENTS.md)** | Supported assistants and where each keeps its settings |
| **[Settings](docs/CONFIGURATION.md)** | Regions, every option, using more than one Inforcer account |
| **[What you can ask](docs/USAGE.md)** | Questions it answers, and fixes when something looks wrong |
| **[Security](docs/SECURITY.md)** | Where your data goes and how your key is handled |

Need a whole tenant documented at once? The
[InforcerCommunity PowerShell module](https://github.com/royklo/InforcerCommunity) does that as a
file, and does it better.
