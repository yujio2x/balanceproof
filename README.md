# BalanceProof

**Turn bank statement PDFs into clean, verified CSV / QuickBooks / OFX / Xero files — without uploading them to anyone.**

BalanceProof is a 100% client-side web app for bookkeepers, accountants and business owners.
Banks often only let you download CSV/Excel exports for the last 12–24 months, but bookkeepers
need older data too — and clients show up with years of statements as PDFs. The usual options are
retyping transactions by hand or uploading confidential client data to a converter service.

BalanceProof parses the PDF **locally in your browser**, checks every parsed transaction against
the statement's running balance, and exports ready-to-import files.

## What it does

- **Deterministic parsing** of common US (MM/DD/YYYY) and UK (DD/MM/YYYY) statement layouts, including card statements without a running balance.
- **Running-balance reconciliation**: every parsed row is mathematically re-checked against the balance column. Rows that don't reconcile are highlighted — you always know if something read wrong.
- **Sign-convention auto-detection**: banks that print debits as positive numbers are handled automatically.
- **Inline editing**: fix any cell before exporting; verification re-runs live.
- **Exports**: generic CSV, JSON, QuickBooks Online CSV, QuickBooks Desktop `.qbo` (OFX 1.02), OFX 2.x (Moneydance/GnuCash), Xero-format CSV.
- **Nothing is uploaded.** No accounts, no server, no analytics. After the page loads you can go offline and keep converting. (Verify it: DevTools → Network.)

## Free vs Pro

| | Free | Pro ($39, one-time) |
|---|---|---|
| Statements | unlimited | unlimited |
| Pages per statement | 3 | unlimited |
| CSV / JSON export | ✓ | ✓ |
| QuickBooks CSV / .qbo / OFX / Xero | — | ✓ |
| Future bank-profile updates | — | ✓ |

Pro is a plain offline license key (`BP1.…`) verified with an embedded ECDSA P-256 public key —
no account, no activation server, works offline. Payment is not wired up in this MVP yet (see
Roadmap); early users get Pro keys directly from the author.

## Run it

No build step, no dependencies to install:

```bash
# any static file server, e.g.
python3 -m http.server 8787        # or: npx serve .
# open http://localhost:8787
```

The only external dependency is the pdf.js engine loaded from jsDelivr CDN
(needed once per page load; the statements themselves are processed locally).

## Development

```bash
node test/run.js     # 73 unit tests: parser, exporters, license verification
```

`test/fixtures/*.txt|pdf` contain synthetic statement layouts (not real bank documents).
`tools/make-fixture-pdf.js` regenerates the PDF fixtures; `tools/serve.js` is a tiny static
server used for local QA.

## Known limits (honest MVP)

- **Scanned/photo PDFs don't work** — there is no text to extract, and BalanceProof deliberately
  does not OCR in the cloud. Text-based PDFs from online banking work.
- Layout coverage is pragmatic, not exhaustive: unusual statement layouts may parse partially.
  Unrecognized lines are always displayed (nothing is silently dropped), and verification tells
  you whether what *was* parsed is arithmetically sound.
- Numeric UK-style dates (`01/03/2024`) are interpreted by a UK/US profile heuristic; statements
  using month names (`01 Mar 2024`) are unambiguous.
- pdf.js loads from a CDN, so first load needs internet.

## Status

**MVP — NOT YET VALIDATED BY REAL USERS.** The parser and exporters are covered by unit tests
and were QA'd end-to-end in a browser; real-world statement diversity has not been tested yet.

## License

Code: MIT (see LICENSE). "QuickBooks" is a trademark of Intuit Inc.; "Xero" is a trademark of
Xero Limited. BalanceProof is not affiliated with, endorsed by, or sponsored by any bank, Intuit,
or Xero.
