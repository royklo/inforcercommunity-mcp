# Security and your data

[← README](../README.md)

The short version:

- It runs on your machine. Your API key stays there.
- It only reads. It cannot change anything in Inforcer unless you turn that on yourself.
- Your tenant data goes to your AI provider, because that is how the assistant answers. Nothing here
  changes that, and you should decide whether that is acceptable for your clients.

## Where your data goes

When you ask a question, tenant data travels from the Inforcer API, through this server, into your
assistant's context. From there it goes wherever your assistant sends things: your AI provider, and
your assistant's own saved conversation history on disk.

**That is the whole point of the tool, and it is worth thinking about if you are handling client
data under a contract.** This server does not strip names, email addresses, or anything else out of
what Inforcer returns. Pretending otherwise would be worse than saying so plainly.

What it does instead is limit how much gets pulled in at once:

- Answers are size-capped, so a question cannot drag an entire tenant into your assistant.
- Lists are capped by default.
- `INFORCER_TENANTS` restricts a setup to named tenants only.

The only network requests it makes are to the Inforcer API, plus a check to npm for a newer version
when it starts. That check sends nothing about you or your tenants, and you can turn it off with
`INFORCER_NO_UPDATE_CHECK=1`.

## Tenant data is not trustworthy input

Anyone who can name a policy, a group, or a user in a tenant you monitor is writing text that reaches
your assistant. They need no access to your own systems to do it.

Every answer is marked as untrusted data before your assistant sees it. **That marking helps, but it
is not a guarantee.** Treat it as a reminder, not a control.

The part worth understanding is what else is in the room. Your assistant may have other tools
connected that can send email, write files, or call an API. Untrusted text from a client tenant sits
in the same session as those tools.

So keep `INFORCER_TENANTS` narrow, and think about what else is connected when you work with tenants
you don't fully control.

Anything this server works out for itself, like translated setting names, is kept separate from what
Inforcer actually returned, so the two can always be told apart.

## Your API key

- It is read from your assistant's settings and used to call Inforcer. This server writes no files of
  its own - no key file, no cache, nothing. A test enforces that.
- `inforcer_status` shows only a short fingerprint of the key, never the key.
- The key is filtered out of the server's log output, including crash messages, and out of successful
  answers as well as errors.

**Two limits worth knowing:**

The filtering is a plain text match on the exact key. It would not catch the key if something encoded
or reformatted it first. It is a safety net, not a guarantee.

Your key sits in your assistant's settings file in plain text. That is how nearly every MCP server
works today, but it means the file is worth protecting. If you use
[an accounts file](CONFIGURATION.md#more-than-one-inforcer-account), `chmod 600` it.

## It cannot change anything

Every Inforcer route that could change something is refused. Not hidden, not discouraged - refused,
with a message saying so.

There is one exception, which is a search that has to be sent as a POST because its filters are too
long for a URL. It reads; it does not write.

You can allow writes by setting `INFORCER_ALLOW_WRITES=1` in your assistant's settings. It is a
setting rather than something the assistant can choose because **only the person who edits that file
can turn it on.** An assistant that has been fed misleading instructions by tenant data cannot enable
it on its own.

The refusal is based on the HTTP method, so a route added in future is refused by default until
somebody classifies it. Getting it wrong fails safe.

`inforcer_request`, the general-purpose read tool, is read-only always, and `INFORCER_ALLOW_WRITES`
does not change that.

## Why it runs on your machine

Your assistant starts this server as a program on your own computer. It is not a service anyone
hosts, and there is no address to connect to.

That keeps three things true:

1. **Your API key never leaves your machine.** There is no shared server holding keys for many MSPs.
2. **The write setting means something.** It is protected by the fact that only you can edit your own
   settings file. That would be weaker on a shared deployment.
3. **Untrusted tenant data stays on one workstation.**

The cost is that cloud-only assistants cannot use it. That is a deliberate trade.
[Which assistants are affected →](CLIENTS.md#assistants-that-cant-use-this)

## What is not promised

Being explicit, so nothing here reads as a guarantee it isn't:

- **This is a community project, maintained by one person.** It has no security audit, no
  certification, and no support commitment. Read the [licence](../LICENSE): it is provided as-is.
- **It does not make Inforcer, or your AI provider, more or less secure.** It only moves data between
  them at your request.
- **The untrusted-data marking does not prevent prompt injection.** No such marking does.
- **It cannot protect data once your assistant has it.** What your AI provider retains, and for how
  long, is between you and them.

If you find a security problem, please
[report it privately](https://github.com/royklo/inforcerCommunity-mcp/security/advisories/new) or
contact [Roy Klooster](https://github.com/royklo) directly. Please do not open an issue - those are
public, so the first report would publish the problem before there is a fix.
