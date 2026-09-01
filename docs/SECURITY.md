# Security and your data

[← README](../README.md) · [Set it up](SETUP.md) · [Examples](EXAMPLES.md)

## Where the data comes from

The Inforcer REST API, and nowhere else. Inforcer reads your clients' Microsoft 365 tenants for you.

This tool has no data of its own. It writes nothing to disk, and keeps answers in memory for a short
time - up to 15 minutes - so that asking twice doesn't mean waiting twice. Close your assistant and
that is gone.

## Where it runs

On your own computer. Your assistant starts it, and stops it when you close the assistant. There is
no hosted service and no address to connect to, so your Inforcer API key never leaves your machine.

## The one thing to keep in mind

**Your clients' data passes through your AI assistant** - that is how you get an answer at all. So
you are sharing it with whoever makes that assistant: Anthropic for Claude, Microsoft and OpenAI for
Copilot. It also stays in your assistant's saved conversations until you delete them.

That is true of anything you connect to an assistant, and it is worth a thought if you handle client
data under a contract.

Three things limit how much arrives at once: answers are size-capped, lists are cut short by default,
and `INFORCER_TENANTS` narrows which clients get looked up. If you want a firm boundary, make it the
key - ask Inforcer for one that covers only the clients you need.
