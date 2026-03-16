# Intelligent Document Intake & Routing — n8n + Claude Vision

> Built with n8n + Claude API (Vision) + Gmail + Tally.
> A production-ready document processing pipeline for a financial advisory firm — classifies, validates, and routes KYC documents automatically.

[Demo on X](https://x.com/0xTrapo/status/2032056589822181421)

---

## What It Does

Financial advisory clients submit documents. This workflow handles everything after submission — no manual sorting, no missed validations, no documents sitting unreviewed in an inbox.

Claude Vision reads the document. The pipeline validates it. The right person gets notified automatically.

**Core behaviour:**
- Accepts document submissions via Gmail or a Tally intake form
- Splits attachments and sends each through Claude Vision for classification
- Extracts structured details from each document type
- Runs parallel validation per document type (passport, statement, POA, fund source, investment)
- Routes the compiled result: approved, soft flag, or failed — with advisor alerts where needed

---

## Architecture — Phase by Phase

```
INTAKE
  Gmail Trigger / Tally Trigger
    → Get a message
    → Split attachments

CLASSIFICATION
  → Simplify label
  → Claude extracts document type     (Claude Vision — POST /v1/messages)
  → Extract label

EXTRACTION
  → Claude extracts document details  (Claude Vision — POST /v1/messages)
  → Cleanup details

VALIDATION
  → Separate docs (Rules node — routes each doc to correct validator)
      ├── Passport validation
      ├── Statement validation
      ├── POA validation
      ├── Fund source validation
      └── Investment validation
  → Merge (append all validation results)

ROUTING
  → Compile status
  → Storage (create record)
  → Switch (Rules)
      ├── Failed submission  → Inform user (Gmail)
      ├── Soft flag          → Contact advisor (Gmail)
      └── Successful         → Approval (Gmail)
```

---

## Document Types Handled

| Document | Validated For |
|---|---|
| Passport | Identity, expiry, completeness |
| Bank Statement | Recency, source clarity |
| Power of Attorney | Validity, scope |
| Source of Funds | Compliance, traceability |
| Investment Document | Completeness, type match |

---

## Stack

| Layer | Tool |
|---|---|
| Automation platform | n8n |
| AI model | Claude Sonnet — Vision (Anthropic API) |
| Intake channels | Gmail, Tally form |
| Storage | n8n Storage node (create record) |
| Notifications | Gmail (user + advisor) |

---

## How to Import

1. Download `Document processing.json` from this repo
2. Open your n8n instance → **Workflows → Import from file**
3. Drop the JSON in
4. Set your credentials: Gmail OAuth, Anthropic API key, Tally webhook
5. Update the validation nodes with your firm's document requirements
6. Activate

---

## What Makes This Production-Ready

This isn't a document classifier. It's a full intake operations layer.

- **Dual intake channels** — Gmail and Tally form submissions both feed the same pipeline. One workflow, two entry points.
- **Claude Vision for classification** — no brittle filename parsing or manual tagging. Claude reads the document and determines type before extraction begins.
- **Parallel validation** — each document type has its own validator running simultaneously. The Merge node compiles all results before any routing decision is made.
- **Three-way routing logic** — the Switch node doesn't just pass/fail. It distinguishes between hard failures (missing/invalid documents), soft flags (needs advisor review), and clean approvals. Each path triggers the appropriate communication.
- **Storage on every submission** — every intake creates a record regardless of outcome. Full audit trail out of the box.
- **Follow-up scope** — since every intake is recorded, team can track failed applications that don't come back and decide a follow up method  

---

## Files

```
intelligent-document-intake/
├── Document processing.json   ← import this into n8n
├── README.md
└── assets/
    └── workflow-screenshot.png
```

---

## Built By

Elvis — AI automation builder. I build client-facing systems with n8n and Claude.

GitHub: [github.com/EarlTrapo](https://github.com/EarlTrapo)
X: [@0xTrapo](https://x.com/0xTrapo)
