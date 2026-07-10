---
name: redact-pii
description: Strip PII/PHI (names, emails, phones, SSNs, card numbers) from text using the Kynth Core kynth_redact tool. Use before storing, logging, sharing, or pasting sensitive text anywhere it shouldn't carry personal data.
---

# Redact PII from text

Use the `kynth_redact` MCP tool. It detects and strips names, emails, phone numbers, SSNs, card numbers, and PHI from text.

## Parameters

- `text` (required) — the text to redact
- `types` (optional) — an array of entity types to redact; **omit it to redact everything detected**. Only pass it when the user explicitly wants to keep some categories (e.g. redact SSNs and cards but keep names).
- `placeholder` (optional) — the replacement token; omit for the default

## Workflow

1. If the sensitive text is in a file, read it and pass the contents as `text`. For a document (PDF/image), extract the text first (e.g. `kynth_parse`), then redact.
2. Call `kynth_redact` with the text. Default to full redaction (no `types`) unless the user scoped it.
3. Return the redacted text. Skim it for anything the user specifically flagged — if a flagged item survived, run a second pass with an explicit `types` entry for it.
4. Never echo the original unredacted values back in your summary — the point is that they stop propagating.

## Billing

3 credits per successful call (1 credit = $0.01), debited from the account wallet only on success; failed calls cost nothing.
