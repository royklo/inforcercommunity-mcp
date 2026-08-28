# Settings

[← README](../README.md)

Everything is set through your assistant's settings file, in the `env` block.
[Where that file lives →](CLIENTS.md#where-each-assistant-keeps-its-settings)

Changes take effect when you restart your assistant.

## The two that matter

### `INFORCER_API_KEY`

Your key from the Inforcer portal.

### `INFORCER_REGION`

Which Inforcer region your key belongs to. Defaults to `uk`.

| Value | Region |
|---|---|
| `uk` | United Kingdom |
| `eu` | Europe |
| `us` | United States |
| `anz` | Australia and New Zealand |

A key works in exactly one region. The wrong one gives you `Access denied due to invalid
subscription key`, which looks identical to a wrong key. If something isn't working, check this
first.

## Optional settings

You will not need most of these.

| Setting | Default | What it does |
|---|---|---|
| `INFORCER_TENANTS` | all | Comma-separated tenant ID numbers. Limits this setup to only those tenants. |
| `INFORCER_PAGE_SIZE` | `50` | Items per page where the Inforcer API supports paging - today, the activity log search only. Between 1 and 500. |
| `INFORCER_RESPONSE_BUDGET_BYTES` | `65536` | Size limit on a single answer, in bytes. Maximum 4 MB. |
| `INFORCER_ALLOW_WRITES` | off | Allows routes that can change things. [Read this first](SECURITY.md#it-cannot-change-anything). |
| `INFORCER_ALLOW_UNKNOWN_ROUTES` | off | Allows reading an Inforcer route newer than this release knows about. |
| `INFORCER_NO_UPDATE_CHECK` | off | Stops the check for a newer version when the server starts. |
| `INFORCER_TOOLS` | `all` | `featured` hides one tool. See below. |
| `INFORCER_ACCOUNTS` | — | More than one Inforcer account. [See below](#more-than-one-inforcer-account). |

The three on/off settings accept `1` or `true`. Anything else leaves them off.

A number outside its allowed range, or an unknown region, stops the server at startup with a message
naming the setting. It does not fail quietly later.

### About `INFORCER_TOOLS`

Some assistants limit how many tools they will show at once. `featured` exists to shrink the list.

**In this release it removes exactly one tool of seventeen**, so it will not rescue an assistant that
is capping the list. It is documented because it exists, not because it will help.

There is no setting for the server address. That is deliberate: a pasted setting pointing somewhere
else would send your API key there.

## More than one Inforcer account

Each Inforcer API key covers one account. If you look after two, you have two keys. **One setup
holds both** - you do not need a second server entry.

Put your accounts in a file:

```json
[
  { "label": "contoso",  "key": "contoso-api-key",  "region": "uk", "tenants": [1001, 1002] },
  { "label": "fabrikam", "key": "fabrikam-api-key", "region": "us" }
]
```

Then point at it, instead of setting `INFORCER_API_KEY`:

```json
"env": { "INFORCER_ACCOUNTS": "~/.inforcer/accounts.json" }
```

A file is better than putting the keys inline. Your assistant's settings file is the one most likely
to get synced between machines or pasted into a support ticket. Lock the accounts file down with
`chmod 600 ~/.inforcer/accounts.json`.

Only `label` and `key` are required. Each account can have its own `region` and its own `tenants`
list, so a UK account and a US account work side by side.

### What changes when you have two

Nothing, until you ask something. Then your assistant will ask **which account you mean** before it
answers:

> This server has 2 Inforcer accounts configured: contoso, fabrikam. Ask which one this
> question is about.

It asks rather than guessing, because an answer about the wrong client looks exactly like a right
one. Name the client in your question and it won't need to ask.

`inforcer_status` lists your accounts with their regions. It never shows a key.

Mistakes are caught when your assistant starts, not on your first question. A missing key, a
duplicate label, an unknown region, or a file that isn't there will all say so immediately.
