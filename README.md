# Kynth Core for Claude Code

A [Claude Code](https://code.claude.com) plugin for financial-document work, powered by [Kynth Core](https://api.kynth.studio). Installing it wires up the [`@kynth/api-mcp`](https://www.npmjs.com/package/@kynth/api-mcp) MCP server (~40 tools) and adds three skills that teach Claude how to use it well:

| Skill | What it does |
| --- | --- |
| `extract-financial-doc` | Routes invoices, receipts, bank/card statements, and tables through the right Kynth endpoint and returns schema-validated JSON — vendor, line items, totals, normalized transactions |
| `redact-pii` | Strips names, emails, phones, SSNs, and card numbers from text before it gets stored, logged, or shared |
| `kynth-api` | General reference — auth, the credit wallet, error shapes, and which of the ~40 tools fits which job (with per-call costs) |

## Install

Inside a Claude Code session (this repo doubles as its own marketplace):

```
/plugin marketplace add kyisaiah47/kynth-claude-plugin
/plugin install kynth-core@kynth
```

Or from your shell:

```bash
claude plugin marketplace add kyisaiah47/kynth-claude-plugin
claude plugin install kynth-core@kynth
```

## Set your API key

Get a key at **[api.kynth.studio](https://api.kynth.studio)** — free, no card, **500 credits every month**. Then make it available to the MCP server:

```bash
export KYNTH_API_KEY=ksk_live_your_key_here
```

(Put it in your shell profile so it survives restarts.) Billing is pay-per-call from a credit wallet, and **you're only charged when a call succeeds** — errors and retries cost nothing.

## Use it

```
> pull the line items out of ./invoices/acme-march.pdf
> redact the PII from this support transcript
> what's my kynth credit balance?
```

Skills are namespaced under the plugin: `/kynth-core:extract-financial-doc`, `/kynth-core:redact-pii`, `/kynth-core:kynth-api`. Claude also invokes them automatically when a task matches.

## Requirements

- Node.js 18+ (the MCP server runs via `npx`)
- A Kynth API key (`KYNTH_API_KEY`)

## License

MIT
