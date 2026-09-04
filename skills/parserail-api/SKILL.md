---
name: kynth-api
description: General reference for the Kynth Core API and its MCP tools — authentication, the credit wallet, error shapes, and which tool fits which job. Use when picking a Kynth tool, debugging a 401/402 error, or answering questions about Kynth Core pricing or capabilities.
---

# Kynth Core API reference

Kynth Core (https://api.kynth.studio) is a hosted API that turns documents and messy text into schema-validated JSON. This plugin exposes it as MCP tools (server `kynth-core`, via `@kynth/api-mcp`).

## Authentication

- The MCP server reads the key from the `KYNTH_API_KEY` environment variable. Keys start `ksk_live_…`.
- Get a key free at https://api.kynth.studio — 500 credits granted at signup, wallet refilled back up to 500 on the 1st of each month, no card required.
- If tools fail with `401 unauthorized`, the key is missing or mistyped. Have the user export `KYNTH_API_KEY` and restart Claude Code.

## Credits

- 1 credit = $0.01. Every capability call debits the wallet at that endpoint's rate — **only on success**. Failed calls cost nothing, so retries are safe.
- `kynth_account` returns the current balance (free, read-only).
- `402 insufficient_credits` = empty wallet: top up at https://api.kynth.studio or wait for the monthly refresh.

## Errors

The API returns a stable envelope: `{ "error": { "code", "message" }, "requestId" }` — the MCP tools surface it as `Kynth Core error [code] (HTTP status): message`. Common codes: `unauthorized` (401), `insufficient_credits` (402), `server_error` (500). Every error carries a request id for support.

## Document input

Document tools take exactly one of: `fileUrl` (public URL), `text`, or `fileBase64` + `fileMimeType` (local files, base64-encoded).

## Tools and costs (credits per successful call)

### Documents
| Tool | Does | Cost |
| --- | --- | --- |
| `kynth_parse` | any document → structured fields (optional `docType` hint) | 10 |
| `kynth_invoice` | invoice → vendor, totals, tax, PO refs, line items | 8 |
| `kynth_receipt` | receipt → merchant, items, totals, expense category | 6 |
| `kynth_statement` | bank/card statement → normalized transactions | 12 |
| `kynth_tables` | every table in a doc → headers + rows | 8 |
| `kynth_resume` | resume/CV → structured candidate profile | 8 |
| `kynth_contract` | contract → parties, term, obligations, risk flags | 12 |
| `kynth_split` | multi-doc scan bundle → classified segments | 10 |
| `kynth_compare` | two doc versions → material changes + risk | 15 |

### Text and data
| Tool | Does | Cost |
| --- | --- | --- |
| `kynth_extract` | pull your named fields out of text | 4 |
| `kynth_structure` | messy input + YOUR JSON Schema → validated output | 6 |
| `kynth_redact` | strip PII/PHI from text | 3 |
| `kynth_classify` | label text against your taxonomy | 2 |
| `kynth_categorize` | batch-label up to 100 items | 3 |
| `kynth_summarize` | summary + key points + action items | 3 |
| `kynth_normalize` | messy records → canonical rows + change log | 5 |
| `kynth_match` | entity resolution across two record sets | 8 |
| `kynth_sentiment` | sentiment, aspects, themes | 2 |
| `kynth_moderate` | text/image vs your policy → allow/review/block | 2 |

### Finance and ops
| Tool | Does | Cost |
| --- | --- | --- |
| `kynth_chargeback` | dispute → representment packet | 15 |
| `kynth_po_match` | invoice vs PO vs receipt 3-way match | 10 |
| `kynth_fraud_flag` | order/transaction → risk score + checks | 6 |
| `kynth_dunning` | overdue invoice → collection sequence | 8 |
| `kynth_quote` | job + rates → itemized quote | 8 |

### Comms and growth
| Tool | Does | Cost |
| --- | --- | --- |
| `kynth_reply` | thread → ready-to-send reply | 5 |
| `kynth_triage` | ticket → priority, category, team, SLA risk | 3 |
| `kynth_minutes` | transcript → minutes, decisions, action items | 6 |
| `kynth_review_reply` | review → brand-safe public response | 3 |
| `kynth_outreach` | enriched lead → outreach sequence | 6 |
| `kynth_enrich` | domain/email → company profile | 5 |
| `kynth_research` | company/topic → cited multi-source brief | 50 |
| `kynth_screen` | lead vs your ICP → qualification verdict | 10 |
| `kynth_rewrite` | copy → your brand voice | 3 |
| `kynth_product_copy` | specs → listing-ready copy | 5 |

### Media and agents
| Tool | Does | Cost |
| --- | --- | --- |
| `kynth_describe` | image → alt text, caption, tags | 3 |
| `kynth_transcribe` | audio → text with speakers + timestamps | 10 |
| `kynth_image` | prompt → generated image | 10 |
| `kynth_speak` | text → speech audio | 5 |
| `kynth_memory` | agent memory: store / search / forget | 2 |
| `kynth_account` | credit balance | free |

## Choosing between overlapping tools

- Known financial doc type → the specialized tool (`kynth_invoice`, `kynth_receipt`, `kynth_statement`); unknown → `kynth_parse`.
- Need output in the user's own schema → extraction tool first, then `kynth_structure`.
- One text, few labels → `kynth_classify`; many items at once → `kynth_categorize`.
- Fields from plain text → `kynth_extract`; fields from a PDF/image → a document tool.
