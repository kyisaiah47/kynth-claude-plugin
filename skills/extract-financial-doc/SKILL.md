---
name: extract-financial-doc
description: Extract structured data from a financial document — invoice, receipt, bank/card statement, or embedded tables — using the Kynth Core MCP tools. Use when the user wants line items, totals, transactions, or tables pulled out of a PDF, image, or pasted text as clean JSON.
---

# Extract a financial document

Route the document through the Kynth Core tool that matches its type. The specialized tools return richer, schema-validated JSON and cost fewer credits than generic parsing.

## 1. Pick the tool

| Document | Tool | Output |
| --- | --- | --- |
| Invoice | `kynth_invoice` | vendor, dates, PO refs, tax, totals, line items |
| Receipt | `kynth_receipt` | merchant, items, totals, payment method, expense category |
| Bank/card statement | `kynth_statement` | account, period, balances, every transaction as a normalized row |
| Tables (any doc, even scanned) | `kynth_tables` | every table as clean headers + rows |
| Unknown / other (EOB, ERA, COI…) | `kynth_parse` | structured fields; pass an optional `docType` hint, e.g. `"invoice"` |
| Multi-document scan bundle | `kynth_split` | what each document is + where it starts and ends; then extract each piece |

If the user needs output in their own shape, run the extraction tool first, then pass the result through `kynth_structure` with their JSON Schema — it validates against required fields and property types and returns a `valid` flag.

## 2. Pass the document

Every tool above takes exactly ONE of these input forms:

- `fileUrl` — a public URL to the PDF or image
- `text` — raw text, if the content is already in the conversation
- `fileBase64` + `fileMimeType` — for local files: read the file, base64-encode it, and pass both fields (`fileMimeType` must be right, e.g. `application/pdf`, `image/png`, `image/jpeg`)

For a local file, encode it in one step:

```bash
base64 -i path/to/invoice.pdf
```

Do not pass a local filesystem path as `fileUrl` — it must be publicly reachable.

## 3. What comes back

Each tool returns schema-validated JSON for its document type — the API validates the extraction against the endpoint's schema before returning it, so fields are typed and consistent across calls. Present the parts the user asked for; offer the full JSON if they want to pipe it somewhere.

## Billing

Calls debit the account's credit wallet **only on success** — a failed call (bad input, timeout, server error) costs nothing, so it is safe to retry. Indicative costs: invoice 8 credits, receipt 6, statement 12, tables 8, parse 10, split 10 (1 credit = $0.01). If a call fails with `402 insufficient_credits`, the wallet is empty — the user can top up at https://api.kynth.studio or wait for the monthly 500-credit refresh.
