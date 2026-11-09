# Set it up

[← README](../README.md) · [Examples](EXAMPLES.md) · [Security](SECURITY.md)

Find your assistant below. Follow only that section.

Everything ends the same way: **close your assistant completely and open it again.** It only reads
its settings when it starts.

Then ask it: **"run inforcer_status"**. If the answer contains `"state": "ok"`, you're done.

## The settings block

Most assistants take this. Replace `your-client-id` and `your-client-secret` with your API client's,
and `uk` with your region. Keep the quote marks.

```json
{
  "mcpServers": {
    "inforcer": {
      "command": "npx",
      "args": ["-y", "inforcercommunity-mcp@latest"],
      "env": {
        "INFORCER_CLIENT_ID": "your-client-id",
        "INFORCER_CLIENT_SECRET": "your-client-secret",
        "INFORCER_REGION": "uk"
      }
    }
  }
}
```

**Using an API key instead?** Swap the two client lines for one: `"INFORCER_API_KEY": "your-api-key"`.
Have both? Use the API client - see [An API client and a key together](#an-api-client-and-a-key-together).

> **If the brackets are fighting you, don't fight back.** Paste whatever is already in the settings
> file into a chat with your assistant, paste this block underneath, and ask it to combine the two.
> If the old contents held keys for other tools, replace those with `xxxx` first - your assistant
> only needs the shape, not the secrets.

**On Windows**, one line changes: `"command": "cmd"`, and `"args": ["/c", "npx", "-y",
"inforcercommunity-mcp@latest"]`. Without it nothing loads, and any log mentions `spawn npx ENOENT`.

## Claude Desktop

The Claude app installed on your computer. It makes the settings file for you:

1. Open Claude's settings. **On a Mac** that is the **Claude** menu in your computer's menu bar, at
   the very top of the screen - not the settings inside the Claude window. **On Windows** they are
   inside the window.
2. **Settings**, then **Developer**.
3. Click **Edit Config**. The file opens, and is created if it was missing.
4. Paste in the block above. If something was already there, add this alongside it.
5. Quit Claude completely and reopen.

## Claude Code

One command in a terminal, with your own client ID, secret and region:

```bash
claude mcp add inforcer -s user \
  -e INFORCER_CLIENT_ID=your-client-id \
  -e INFORCER_CLIENT_SECRET=your-client-secret \
  -e INFORCER_REGION=uk \
  -- npx -y inforcercommunity-mcp@latest
```

With an API key, use `-e INFORCER_API_KEY=your-api-key` in place of the two client lines.
`-s user` makes it available in all your projects. On Windows, put `cmd /c` before `npx`.

To remove it later: `claude mcp remove inforcer`.

## VS Code

[One click from the README](../README.md#quickest-way-in) - one button for an API client, one for a
key. It asks for your client ID and secret (or your key), then your region, and keeps the secret in
VS Code's own password store rather than in a file. The tools appear in
**Agent** mode only, not in ordinary Copilot Chat.

**VS Code asks once and never again**, even if you click the button a second time - so a wrong key, secret or
region keeps being used, quietly. To replace it:

1. Press **Cmd+Shift+P** (Mac) or **Ctrl+Shift+P** (Windows).
2. Type `MCP: Open User Configuration`, press Enter.
3. Hover your mouse over the faded `= **********` next to your secret or key. An
   **Edit | Clear | Clear All** bar appears.

![The VS Code settings file, with the Edit, Clear and Clear All bar showing above the API key
line](images/vscode-edit-key.png)

By hand instead, VS Code uses `servers` where everything else uses `mcpServers`, and needs
`"type": "stdio"` inside the block. That difference is the mistake most likely to cost you an
afternoon.

## Cursor, Windsurf, and anything else

The block above, in the assistant's own settings file:

| Assistant | Settings file |
|---|---|
| Cursor | `~/.cursor/mcp.json` |
| Windsurf | `~/.codeium/windsurf/mcp_config.json` |
| Claude Code | `~/.claude.json`, or the command above |
| Claude Desktop (macOS) | `~/Library/Application Support/Claude/claude_desktop_config.json` |
| Claude Desktop (Windows) | `%APPDATA%\Claude\claude_desktop_config.json` |
| VS Code | `.vscode/mcp.json` - different format, see above |
| Codex | `~/.codex/config.toml` - different format, see below |

`~` is your home folder. **Folders starting with a dot are hidden**, so you won't see them by
browsing: on a Mac press **Cmd+Shift+G** in Finder and paste the path; on Windows paste it into the
File Explorer address bar. If the file doesn't exist yet, create an empty one with that exact name.

Paths verified against each vendor's own documentation - see [Sources](#sources).

## Codex

Codex uses its own format:

```toml
[mcp_servers.inforcer]
command = "npx"
args = ["-y", "inforcercommunity-mcp@latest"]

[mcp_servers.inforcer.env]
INFORCER_CLIENT_ID = "your-client-id"
INFORCER_CLIENT_SECRET = "your-client-secret"
INFORCER_REGION = "uk"
```

With an API key, `INFORCER_API_KEY = "your-api-key"` replaces the two client lines. Or let Codex
write it: `codex mcp add inforcer --env INFORCER_CLIENT_ID=your-client-id --env
INFORCER_CLIENT_SECRET=your-client-secret --env INFORCER_REGION=uk -- npx -y
inforcercommunity-mcp@latest`. Run `/mcp` to confirm it loaded.

## If it isn't working

Ask your assistant to **run `inforcer_status`** first. It checks everything and reads none of your
clients' data.

**Nothing happened at all?** In this order: did you fully close and reopen the assistant; is it one
that [can do this](../README.md#what-you-need); is Node.js installed; did the settings go in the
right file; on Windows, did you make the `cmd /c` change.

| What you see | What it means |
|---|---|
| `Access denied due to invalid subscription key` | The **region** is wrong, or the key is. Check the region first - it fails in exactly this way. |
| `Token request failed (HTTP 400)` or `(HTTP 401)` | The client ID or secret is wrong, or the API client has expired or been switched off. Check the region too. |
| `"state": "valid-no-scope"` | Fine. Your key works, it just isn't allowed to read one particular thing. |
| A message about missing permissions | Your key or API client is missing one, and the message names it. An API client's permissions are on its **Permissions** tab in the Inforcer portal. |
| `pass the Microsoft tenant ID (GUID) or name` | An API client knows clients by their Microsoft tenant ID, not by Inforcer's ID number. Name the client instead. |
| `"apiKey": "ignored ..."` in the status | You gave an API client and a key for the same account. The API client is used and the key is not. Remove the key line to tidy up. |
| The first question takes ~25 seconds | Normal. Inforcer is slow the first time it is asked about a client. For the next five minutes, questions about that client are fast. Each client pays its own first-time cost. |
| An answer looks cut off | Expected on a big client. Narrow the question. |
| It keeps asking which account | You have two Inforcer accounts set up. Name the client in your question. |
| It refuses to change something | It only reads. Every request that could change something is refused. |
| Worked yesterday, not today | Restart the assistant. With `@latest` it collects the newest copy on each start, which is also how a fix reaches you. |

Still stuck? [Open an issue](https://github.com/royklo/inforcerCommunity-mcp/issues) with what you
tried and what it said. **Issues are public** - never paste your key or client secret, and ask your assistant to strip
client names first.

## Removing it

Delete the `"inforcer"` block from the settings file and restart. This tool creates no files of its
own, so nothing of it is left behind - but your assistant's saved conversations still contain client
data, and you delete those in your assistant.

## Optional settings

Extra lines inside `"env"`. You will probably never need any of them.

| Setting | Default | What it does |
|---|---|---|
| `INFORCER_TENANTS` | all | Which clients to look up, comma-separated: Inforcer's ID numbers with a key, Microsoft tenant IDs with an API client. The everyday tools then look up only those clients. A narrowing rather than a firm boundary - for that, limit the API client to those clients in the Inforcer portal (its **Filters** tab), or ask Inforcer for a key covering only them. Ask *"list my tenants with their IDs"* to find them. |
| `INFORCER_ACCOUNTS` | — | Two or more Inforcer accounts at once. [See below](#two-inforcer-accounts). |
| `INFORCER_RESPONSE_BUDGET_BYTES` | `65536` | How big one answer may get, in bytes - roughly 65,000 characters, about twenty pages. Raise it if answers keep getting cut short. Maximum 4 MB. Assessment results always get at least 1 MB, whatever this is set to. |
| `INFORCER_PAGE_SIZE` | `50` | Items per batch on the parts of Inforcer that work in pages - the activity log search, and user and group listings. Between 1 and 500. The activity log search takes at most 100, so anything above 100 means 100 there. |
| `INFORCER_TOOLS` | `all` | `featured` shows a shorter tool list. In this release it hides one tool only, so it will not rescue an assistant that caps the list. |
| `INFORCER_ALLOW_UNKNOWN_ROUTES` | off | Lets it read something Inforcer added after this version was released. |
| `INFORCER_NO_UPDATE_CHECK` | off | Stops it checking for a newer version of itself at startup. |
| `INFORCER_ALLOW_WRITES` | off | Nothing to switch on - Inforcer has no way to change a client's settings, so neither does this. |

The three on/off settings accept `1` or `true`. Anything else leaves them off, silently - so check
your spelling. A bad number, region or tenant list is different, and so is a client ID without its
secret or an `INFORCER_ACCOUNTS` that can't be read: the tool refuses to start and names the setting.

There is deliberately **no setting for the Inforcer address**. If there were, a settings block pasted
from somewhere untrustworthy could point this at someone else's server and send your key or client secret there.

### Two Inforcer accounts

Each API client or key covers one Inforcer account. One setup can hold two. Put them in a file of
their own - `accounts.json`, anywhere you like:

```json
[
  { "label": "contoso",  "clientId": "contoso-client-id", "clientSecret": "contoso-client-secret", "region": "uk",
    "tenants": ["00000000-0000-0000-0000-000000001001"] },
  { "label": "fabrikam", "key": "fabrikam-api-key", "region": "us", "tenants": [1001, 1002] }
]
```

Then, instead of the client or key lines:

```json
"env": { "INFORCER_ACCOUNTS": "~/.inforcer/accounts.json" }
```

Each entry needs a `label`, plus `clientId` and `clientSecret`, or `key`. An entry with all three uses
the API client and ignores the key. `tenants` takes
Microsoft tenant IDs for an API client and Inforcer's ID numbers for a key. A separate file is safer
than putting secrets in the settings block, which is the file most likely to get synced or pasted
into a support ticket. Make it readable only by you: `chmod 600` on a Mac, or Properties → Security on
Windows.

With two accounts your assistant asks **which one you mean** before answering, rather than guessing -
an answer about the wrong client looks exactly like a right one. Name the client and it won't ask.

### An API client and a key together

If one account has both an API client and a key, only the API client is used. The key is ignored:
nothing is sent with it, and the status check says so. Settings that already hold both keep working,
so there is no rush - remove the key line when it suits you.

Inforcer's newer API, the one an API client uses, knows clients by their Microsoft tenant ID, never by
Inforcer's ID number. Name the client in your question, or give that ID.

## Keeping it up to date

Every example ends in `@latest`, so your assistant picks up fixes when it starts - not while it is
running. Pin an exact version instead, like `inforcercommunity-mcp@1.4.2`, if you would rather
approve each update.

## Sources

- [Claude Desktop config location, modelcontextprotocol.io](https://modelcontextprotocol.io/docs/develop/connect-local-servers)
- [MCP servers in VS Code](https://code.visualstudio.com/docs/copilot/customization/mcp-servers)
- [VS Code install-link scheme](https://code.visualstudio.com/api/extension-guides/ai/mcp)
- [MCP in Codex](https://learn.chatgpt.com/docs/extend/mcp?surface=cli)
- [MCP in Cursor](https://cursor.com/docs/mcp)
- [Cascade MCP integration, Windsurf](https://docs.windsurf.com/windsurf/cascade/mcp)
- Windows `cmd /c`: [modelcontextprotocol/servers#3460](https://github.com/modelcontextprotocol/servers/issues/3460)
  and [anthropics/claude-code#58510](https://github.com/anthropics/claude-code/issues/58510)
