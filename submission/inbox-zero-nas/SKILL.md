---
name: inbox-zero-nas
description: A local Inbox Zero triage that classifies a NAS inbox into invoices, contracts, receipts, screenshots, archives, and misc, then delivers a confirmed rename-and-archive plan plus a Markdown digest. Use when the user asks to clear Downloads, empty an inbox folder, auto-sort messy files, or reach Inbox Zero on UGREEN AI NAS.
---

# Goal

Help a knowledge worker clear one authorized NAS inbox folder. Produce: (1) a classification table with proposed destination folders and filenames, (2) after confirmation, renamed files moved into category folders under an archive root, (3) a Markdown digest saved on the NAS. Cap one run at 80 files. Never delete originals. Never invent categories from empty scans. Prefer local NAS document extraction over cloud-only organizers.

Read [references/classification-rules.md](references/classification-rules.md) before naming or routing files. Read [references/safety.md](references/safety.md) before any state-changing step. Read [references/disclosures.md](references/disclosures.md) for third-party and AI disclosures.

# Required input

Collect only:

1. Inbox folder: a user-provided absolute NAS folder path, or an explicit request to use the personal Downloads folder.
2. Archive root: destination parent folder for category folders. If omitted, propose `Organized` as a sibling of the inbox folder and wait for confirmation.
3. Mode: `preview-only` (default) or `apply-after-confirm`.
4. Optional naming preference: keep original basename as suffix, or fully replace with structured names.

Do not pre-collect private CLI parameters owned by child experts. Do not ask for credentials, API keys, or passwords.

# Generation-time capability evidence

- `ug-filesystem` → `file-agent`
- `ug-filemgr` → `file-agent`
- `file-workflow` → `file-agent`
- `ug-document` → `document-agent`
- `ug-knowledge` → `knowledge-agent`

# Workflow

Execute in order. Every downstream step may use only verified business facts from an upstream `completed` result. If a required absolute path, file list, extraction table, or save path is missing, stop and report the gap. Do not call `cli.invoke` from this parent Skill.

## 1. Resolve scope

If the user already gave an absolute inbox path, keep it. If they asked for personal Downloads without a path:

`delegate --target file-agent --instruction 'Resolve the current user personal home directory, then locate the Downloads folder under that home. Return the verified absolute Downloads path. If Downloads is missing, return no results and stop.'`

On `completed`, continue only with the verified absolute inbox path. On `failed` or `cancelled`, stop and report. On `waiting_user` or `waiting_approval`, wait for the same delegation to resume; conversational agreement is not platform approval.

## 2. Inventory the inbox

`delegate --target file-agent --instruction 'List direct children of the verified absolute inbox folder path supplied for this run. Return each entry name, type, size, and modification time. If the folder is empty or the listing has no results, report an empty result and stop. If more than 80 files are present, return only the first 80 sorted by newest modification time and note the overflow count. Do not modify files.'`

Empty-result rule: if the listing is an empty result, zero matches, or the directory is empty, tell the user the inbox is already clear and stop. Do not broaden to other folders.

Handle `completed`, `failed`, `cancelled`, `waiting_user`, and `waiting_approval` as in step 1.

## 3. Extract document signals

From the inventory `completed` result, select document-like files (pdf, doc, docx, txt, md, png/jpg scans that look like paperwork). If none, skip to step 4 with type heuristics only.

`delegate --target document-agent --instruction 'For the verified absolute document file paths returned by the inbox inventory, extract classification signals for Inbox Zero. Prefer schema mixed when types vary. For each file return: detected type among invoice, contract, receipt, expense, or other; vendor or counterparty if present; document date if present; amount if present; short title; confidence; and a proposed basename using YYYY-MM-DD_type_vendor_amount when fields exist. Deduplicate near-identical invoices. Do not write or move files. If a file cannot be read, mark it needs-review instead of guessing.'`

If this delegation returns no results for all documents, keep those files as `misc` with confidence low. Handle all five public states as in step 1.

## 4. Build the preview plan

Combine inventory + extraction into a table: source path, category (`invoices`, `contracts`, `receipts`, `screenshots`, `archives`, `documents`, `misc`), proposed filename, destination folder under the archive root, confidence, and notes. Apply [references/classification-rules.md](references/classification-rules.md).

Show the table to the user. State how many gateway or workflow approvals will appear if they choose apply. Default remains `preview-only`.

If mode is `preview-only`, stop after the table. That is a successful preview completion.

## 5. Apply after confirmation

Continue only when the user explicitly confirms apply for specific rows. Never treat silence as approval.

`delegate --target file-agent --instruction 'Using only the user-confirmed rows from the Inbox Zero plan, create any missing destination category folders under the verified absolute archive root, rename each confirmed source file to the confirmed new basename when needed, then move each confirmed file into its confirmed destination folder. Skip every row the user rejected. Do not delete files. Do not overwrite without the conflict policy the user confirmed. Return each successful final absolute path and every skipped or failed item separately.'`

Moves, renames, and folder creation may enter `waiting_approval`; wait for platform approval on the same delegation. On `failed` or `cancelled`, keep already confirmed successes, do not auto-retry effectful operations, and report duplication risk before any manual retry.

Optional tags after successful moves:

`delegate --target file-agent --instruction 'For each verified absolute file path successfully moved in the Inbox Zero apply step, bind the single tag inbox-zero-sorted when the user opted into tagging. Create the tag if needed. Do not invent extra tags per vendor. Return tag bind results per file.'`

## 6. Save the digest

`delegate --target knowledge-agent --instruction 'Save a Markdown Inbox Zero digest for this run using only verified facts: inbox path, archive root, counts per category, each moved final path, skipped items, failures, and leftover inbox files. Use filename hint inbox-zero-digest. Save under the verified absolute archive root when provided. Return whether save verified succeeded and the saved path. Do not claim knowledge-index readiness.'`

If `verified` save is missing, report that organization may have succeeded but the digest was not confirmed.

# Completion and failure

- `completed`: continue only when required business facts for the next step exist.
- `failed`: stop dependents; report expert, safe error facts, and prior confirmed results.
- `cancelled`: report incomplete work; do not auto-switch experts or auto-retry effectful steps.
- `waiting_user`: wait for the user answer and resume the same delegation.
- `waiting_approval`: wait for platform approval; chat agreement is not approval; check the eventual terminal result after resume.
- Empty inventory: report no results and stop.
- Partial runs: separately list completed, failed or cancelled, waiting, and dependency-blocked steps.
- Success output: preview table and/or final moved paths plus digest path when verified.
