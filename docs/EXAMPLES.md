# Examples

[← README](../README.md) · [Set it up](SETUP.md) · [Security](SECURITY.md)

Real questions, and what comes back. Use client names, not ID numbers. The names and numbers below
are made up; the shape of every answer is real.

---

### *"List my tenants with their alignment scores."*

| Client | Baseline alignment | Secure score |
|---|---|---|
| Contoso | 91.5% | 412 / 600 |
| Fabrikam | 80.9% | 388 / 600 |
| Northwind | 57.9% | 301 / 600 |

### *"Which three should I book a review with first, and why?"*

> Northwind first - 57.9% is the widest gap in your portfolio, and its secure score is lowest too.
> Then Fabrikam, which has drifted since its last alignment run. Contoso is in good shape.

You get the numbers, not a report, so your assistant can rank and reason about them - and give you
the same answer *"as a table"* or *"as a summary I can send the client"* on request.

### *"Which policy in Contoso controls password expiry?"*

> **Entra - Password Policy - Standard.** Password expiry is disabled (never expires), which matches
> the assigned baseline.

Found by looking **inside** the policies, not at their names. That matters - see the last section.

### *"Run the Copilot Readiness assessment on Contoso."*

```
21 checks: 16 passed, 4 failed, 1 not applicable

Failed:
  External sharing is disabled on all SharePoint sites
    3 sites have external sharing enabled (New and existing guests):
    Communication site, Project Falcon, Marketing
  Legacy authentication is blocked
  ...
```

One client at a time, back in about ten to fifteen seconds. A report covers up to 25 clients; ask for
the next batch after that.

### *"Welke van mijn klanten wijken het meest af van hun baseline?"*

> Northwind wijkt het sterkst af, met 57,9% afstemming. Daarna Fabrikam op 80,9%.

**Any language works** - Dutch, German, French, Spanish. Nobody had to build that: what this sends
your assistant is data rather than finished sentences, so your assistant writes the answer, in the
language you asked in. Policy names stay as they are in the tenant.

---

### More you can ask

| | |
|---|---|
| *"Where does Contoso deviate from its assigned baseline?"* | Every difference, per baseline |
| *"Show me Contoso's Conditional Access policies"* | Just those, not all 300 |
| *"Who holds Global Administrator in Contoso?"* | The role, and who is in it |
| *"What licences does jane@contoso.example have?"* | One person |
| *"Which groups exist in Contoso, and who's in them?"* | Groups and members |
| *"What changed in Contoso in the last 30 days?"* | The activity log |
| *"What's Contoso's secure score, and is it improving?"* | Score plus history |
| *"Run the Global Admins report for Contoso"* | A report, collected and returned |

---

## Questions about the answers

### *"I searched for a policy and got nothing back. So nothing configures it?"*

**No.** You have learned one thing only: **no policy is named that.** Policy names rarely describe
their contents - a policy that turns off passkeys might be called anything at all.

> ✅ *"Which policy in Contoso actually controls password expiry?"*
> ❌ *"Is there a policy called password expiry?"*

Asked the first way, your assistant searches inside the policies. Even then it finds *a* policy that
controls the setting - never proof that nothing else does.

### *"The check passed, but the message says 1 out of 27. Which do I believe?"*

Both - they answer different things. The verdict is the check's own, not a tally of everything it
looked at, so **ask for the ratio, never just the tick.**

Don't read those two numbers as a count of things either: one line of a result can stand for several
sites or policies, so *"0 out of 1"* may mean a handful failed. When it matters, ask for the names.

And "not applicable" is not a failure - ask for all three numbers, passed, failed and not applicable.

### *"Can it change anything in a client's tenant?"*

Not today. Every request that could change something is refused.

### *"Can it document a whole tenant for me?"*

No - one client's policies are more than any assistant can hold at once. The
[PowerShell module](https://github.com/royklo/InforcerCommunity) writes that to a file instead.
