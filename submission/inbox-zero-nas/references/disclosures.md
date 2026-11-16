# Disclosures

## Principal functionality developed for this Hackathon

Original orchestration Skill `inbox-zero-nas`: Inbox Zero triage for an authorized UGREEN AI NAS folder, combining file inventory, document extraction, confirmed rename/move, optional tagging, and a Markdown digest. The core workflow and wording were newly authored for UGREEN Hackathon 2026 on AI NAS.

## Third-party components and licenses

| Component | Source | License / notes | Role |
| --- | --- | --- | --- |
| UGREEN / Uliya Skill Development Standard and capability catalog | Official UGREEN Skill Developer Package (`uliya-skill-kit`) | Used as required platform documentation; not redistributed inside the Skill ZIP | Capability evidence, delegate targets, checker |
| No npm/PyPI runtime libraries inside the Skill package | n/a | n/a | Skill ZIP contains Markdown only |

This Skill package contains no scripts, binaries, manifests, credentials, or API keys.

## AI tools used

| Tool | Principal use |
| --- | --- |
| Cursor AI coding agent | Draft and refine `SKILL.md`, references, Round 1 proposal text, packaging, and static checker iteration |
| UGREEN NAS built-in expert agents at runtime (`file-agent`, `document-agent`, `knowledge-agent`) | Perform real NAS reads/writes after delegation on UGOS / Uliya |

No external model API keys are embedded. Runtime model choice follows the NAS / Uliya platform configuration.
