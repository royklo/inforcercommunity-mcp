# What you can ask

[← README](../README.md)

You ask questions in plain English. Your assistant works out which tool to use. You never need to
learn a command or an ID.

## Things it can answer

| Ask something like | About |
|---|---|
| "List my tenants and their alignment scores" | All your tenants at a glance |
| "Which tenants are furthest from their baseline?" | Alignment across the board |
| "Where does Contoso deviate from its assigned baseline?" | One tenant, in detail |
| "What baselines do I have, and what's in them?" | Your baselines |
| "Show me Contoso's Conditional Access policies" | Policies, filtered |
| "Which policy in Contoso controls password expiry?" | Finding the policy behind a setting |
| "Who holds Global Administrator in Contoso?" | Directory roles and members |
| "What licences does jane@contoso.com have?" | One user |
| "Which groups exist in Contoso, and who's in them?" | Groups and membership |
| "What's Contoso's secure score, and is it improving?" | Secure score, with history |
| "What changed in Contoso in the last 30 days?" | The activity log |
| "What reports have run recently?" | Report runs and types |

Use tenant names like "Contoso" rather than ID numbers. Group names work the same way.

Ask for the shape you want, too. *"...as a table"*, *"...as a summary for a client"*, or *"just the
names"* all work, because your assistant formats the answer itself.

**Ask in your own language.** This returns raw data, not sentences, so your assistant answers in
whatever language you asked in. Dutch question, Dutch answer. The tenant's own data, like policy
names, stays exactly as Inforcer stored it.

## Getting trustworthy answers

### "No results" is not the same as "it isn't configured"

If you ask *"is there a policy called X?"* and get nothing back, that means **no policy has that
name**. It does not mean nothing in the tenant does that thing.

Policy names rarely describe what is inside them. A policy that turns off passkeys might be called
anything at all, so searching for "passkey" by name can easily come back empty while the setting is
very much in place.

So when you want to know what controls something, ask about the **setting**, not the name:

> ✅ *"Which policy in Contoso actually controls password expiry?"*
> ❌ *"Is there a policy called password expiry?"*

Phrased the first way, your assistant searches inside the policy settings, which is the only way to
answer it properly.

### When a setting is a number, check what it counts

Intune stores its settings under machine-readable names like
`..._deliveryoptimization_dodownloadmode_1`. Those are unreadable, so this translates them into the
names you see in the Intune portal, such as "DO Download Mode".

Where that matters is numbers. You might get back:

```
DO Max Cache Age = 7
```

Seven what? Days, hours, minutes? The name doesn't say, and neither does anything else Inforcer
returns. **The answer is seconds**, and the only place that word appears is Microsoft's longer
description of the setting.

Those descriptions are left out by default, because including every one of them can triple the
length of an answer. So if a number matters to your decision, ask for them:

> *"Include the setting descriptions."*

You don't have to remember this. When an answer contains settings whose descriptions were left out,
it says so, and tells you how many.

**One limit:** this translation only applies to Intune Settings Catalog policies and macOS
configuration profiles. Compliance policies, Entra, Exchange, SharePoint and Conditional Access
don't use these setting names, so there is nothing to translate and none is shown.

## Big tenants

A tenant can hold hundreds of policies, far more than fits in one answer. Your assistant narrows the
search first, then tells you what it found and what it left out, so a short answer is never mistaken
for the whole picture.

If an answer looks cut off, narrow the question. "Contoso's Conditional Access policies" works where
"all Contoso's policies" won't.

### Documenting a whole tenant

Don't use this for that. Every policy in one tenant is roughly 477,000 words of context, which no
assistant can hold.

The [InforcerCommunity PowerShell module](https://github.com/royklo/InforcerCommunity/blob/main/docs/CMDLET-REFERENCE.md#export-inforcertenantdocumentation)
writes it to a file instead:

```powershell
Install-Module InforcerCommunity -Scope CurrentUser
Connect-Inforcer -ApiKey <key> -Region uk

# a whole tenant, as HTML, Markdown or Excel
Export-InforcerTenantDocumentation -TenantId <id> -Format Html -OutputPath .

# a tenant compared against a baseline
Compare-InforcerEnvironments -SourceBaselineId <baseline> -DestinationTenantId <id> -OutputPath .
```

The module also reads Microsoft Graph, which this cannot, so it fills in things like group names
behind assignments.

## If something isn't working

Ask your assistant to **run `inforcer_status`** first. It checks everything and reads none of your
tenant data.

| What you see | What it usually means |
|---|---|
| `Access denied due to invalid subscription key` | The **region** is wrong, or the **key** is. [Check the region](CONFIGURATION.md#inforcer_region). On VS Code, [check which key it kept](CLIENTS.md#changing-your-key-later). |
| Your assistant doesn't know what `inforcer_status` is | Settings in the wrong file, or it hasn't restarted. [Find the file](CLIENTS.md#where-each-assistant-keeps-its-settings). |
| On Windows: nothing loads, or a log mentions `spawn npx ENOENT` | `npx` needs `cmd /c` in front of it. [The Windows block](CLIENTS.md#on-windows). |
| `"state": "valid-no-scope"` | Not a problem. Your key works, it just can't read one particular thing. |
| A message about missing permissions | Your key lacks a scope. The message names which one to ask Inforcer for. |
| Some tools are there, others are missing | Your assistant limits how many tools it shows. There is no good fix from this side today; ask it to list the tools it can see so you know what you have. |
| It keeps asking which account | You have more than one configured. Name the client in your question. |
| The first question takes about 25 seconds | Normal. Inforcer is slow the first time. For the next five minutes it's instant. |
| Something felt slow and you want to know why | On a slow answer the tool reports two timings: how long it waited for Inforcer, and how long it spent sorting the results itself. Ask your assistant which was bigger. |
| An answer looks cut off | Expected on a big tenant. Narrow the question, or [use PowerShell](#documenting-a-whole-tenant). |
| It refuses to change something | It's read-only on purpose. [Why](SECURITY.md#it-cannot-change-anything). |

Still stuck? [Open an issue](https://github.com/royklo/inforcerCommunity-mcp/issues) with what
`inforcer_status` said, minus anything you would rather not share.
