# Classification rules

Use these routing rules after document extraction and file-type signals are available. Prefer extraction type when confidence is high. Prefer extension heuristics when extraction is unavailable.

## Category map

| Category | When to use | Destination folder | Filename preference |
| --- | --- | --- | --- |
| `invoices` | Extracted type invoice, or PDF/text clearly billed by a vendor | `Invoices/` | `YYYY-MM-DD_invoice_vendor_amount` |
| `contracts` | Extracted type contract, or agreement/NDA/SOW language | `Contracts/` | `YYYY-MM-DD_contract_counterparty_title` |
| `receipts` | Extracted type receipt/expense, short proof-of-purchase | `Receipts/` | `YYYY-MM-DD_receipt_vendor_amount` |
| `screenshots` | png/jpg/webp with screenshot-like names or UI captures without paperwork fields | `Screenshots/` | keep original basename unless user asked to normalize |
| `archives` | zip/7z/rar/tar/gz packages | `Archives/` | keep original basename |
| `documents` | Readable office/PDF content that is not invoice/contract/receipt | `Documents/` | `YYYY-MM-DD_document_title` when a title exists |
| `misc` | Unknown, encrypted, unsupported, or low-confidence leftovers | `Misc/` | keep original basename |

## Confidence policy

- High: extracted schema matches and key fields exist. Eligible for apply after one confirmation.
- Medium: type likely from filename/extension, weak body signals. Eligible for apply only if the user accepts medium rows.
- Low / needs-review: unreadable, conflicting signals, or possible front/back scan pair. Preview only unless the user explicitly forces a category.

## Collision policy

If the destination already contains the proposed basename, append `-2`, `-3`, and so on. Never overwrite unless the user explicitly chose overwrite for that run.

## Batch limits

- Maximum 80 files per run.
- Maximum 60 documents sent to document extraction in one delegation.
- Overflow files remain in the inbox and are listed in the digest as leftovers.
