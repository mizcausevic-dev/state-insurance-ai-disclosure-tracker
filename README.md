# state-insurance-ai-disclosure-tracker

> **State Insurance AI Disclosure Tracker v0.1 draft.** A per-state lifecycle ledger of US state DOI AI bulletins, circular letters, regulations, and statutes — from `proposed` to `adopted-bulletin` / `enacted-awaiting-effective` to `effective` to `amended` / `superseded` / `sunset` / `withdrawn`. The Operator surface an insurer, reinsurer, MGA, or AI vendor queries to know **which state regulatory set governs an underwriting / claim / rating decision on a given date**.

Part of the [Kinetic Gain Protocol Suite](https://suite.kineticgain.com).

> Status: v0.1 draft. Schema at [`schema/disclosure-event.schema.json`](./schema/disclosure-event.schema.json), per-state streams at [`states/`](./states/), Node verifier at [`src/verify.mjs`](./src/verify.mjs).

## Why this exists

The **NAIC AI Model Bulletin (Nov 2023)** is a model — state DOIs adopt it (or don't) on their own timelines. Each state can also issue independent guidance (NY DFS Circular Letter No. 7, CA DOI Bulletin 2022-5), pass independent statutes (CO SB 21-169), or pre-rule guidance pending formal regulation. As of 2026, 20+ state DOIs have adopted the NAIC bulletin in some form, but the scope, covered lines of business, and obligations differ. An insurer underwriting a homeowner's policy in CT vs CO vs CA vs NY needs different evidence packages on different dates. The lifecycle ledger answers "what is in force for this state on this date" deterministically.

## The shape

| Field | Purpose |
| --- | --- |
| `event_id`, `state`, `timestamp` | Append-only state-stamped identity |
| `lifecycle_state` | One of 9 states (proposed → in-comment-period → adopted-bulletin / enacted-awaiting-effective → effective → amended → superseded → sunset → withdrawn) |
| `regulatory_vehicle` | One of 6 vehicles (NAIC adoption, state DOI independent bulletin, state DOI circular letter, state DOI regulation, state legislative statute, state DOI pre-rule guidance) |
| `citation` | Short label + long title + jurisdictional URI + session year |
| `effective_date` / `sunset_date` | REQUIRED when lifecycle_state is `effective` / `sunset` |
| `scope` | Covered decisions (underwriting / claims-triage / claims-adjudication / rating-pricing / policy-renewal / fraud-detection / marketing-targeting / agent-recommendation) + covered lines of business + covered actors + premium threshold |
| `obligation_kinds` | 14-doctrine obligation taxonomy |
| `regulator` | Primary agency code + name + URI + concurrent jurisdiction |
| `supersedes_event_id` | REQUIRED when lifecycle_state is `superseded` / `amended` |

## Lifecycle state machine

```
proposed                  -> in-comment-period, adopted-bulletin, enacted-awaiting-effective, withdrawn
in-comment-period         -> adopted-bulletin, enacted-awaiting-effective, withdrawn
adopted-bulletin          -> effective, amended, superseded, sunset, withdrawn
enacted-awaiting-effective -> effective, amended, withdrawn
effective                 -> amended, superseded, sunset
amended                   -> effective, amended, superseded, sunset
superseded                -> amended, effective
sunset / withdrawn        -> (terminal)
```

## Seed coverage (v0.1)

| State | Last lifecycle_state | Key citation |
| --- | --- | --- |
| **US-CT** | `effective` (2024-02-26) | CT Notice 2024-1 — first state NAIC AI Model Bulletin adoption |
| **US-NY** | `effective` (2024-07-11) | NY DFS Circular Letter No. 7 (2024) — strictest US insurance AI guidance |
| **US-CO** | `effective` (2023-11-14) | CO 3 CCR 702-10 Reg 10-1-1 (Life) — CO SB 21-169 implementation |
| **US-CA** | `effective` (2022-06-30) | CA DOI Bulletin 2022-5 — anti-bias pre-rule guidance |
| **US-WA** | `effective` (2024-05-01) | WA OIC Technical Assistance Advisory 2024-04 — NAIC bulletin adoption |

Additional states (IL, NH, NV, OK, RI, VT + more) added per PR. Maintain alphabetical filenames (`US-XX.ndjson`), one event per line, timestamps ascending.

## Quick start

```bash
npm install
npm run verify   # validates every state stream against schema + state machine
```

Exit 0 on success, 1 on schema failure, 2 on illegal transition, 3 on broken supersedes reference, 4 on IO error.

## Composes with

| Repo | Role |
| --- | --- |
| [`insurance-decision-record-audit-stream`](https://github.com/mizcausevic-dev/insurance-decision-record-audit-stream) | Per-decision audit events that must conform to the obligation_kinds effective at the time |
| [`state-real-estate-ai-disclosure-tracker`](https://github.com/mizcausevic-dev/state-real-estate-ai-disclosure-tracker) | Sibling PropTech state tracker |
| [`state-ai-disclosure-state-tracker`](https://github.com/mizcausevic-dev/state-ai-disclosure-state-tracker) | Sibling EdTech state tracker |
| [`fda-samd-classification-board`](https://github.com/mizcausevic-dev/fda-samd-classification-board) | Sibling HealthTech regulatory-lifecycle Operator |

## Compliance posture

InsurTech-readiness scaffolding for state DOI AI regulatory tracking. Does not establish compliance with any state regulation — local insurance counsel + state DOI engagement remain authoritative. Per the standing public-language guardrail: *readiness · evidence · posture · controls · scaffolding* — never "state-DOI-compliant" without an external attestation specific to the jurisdiction.

## License

MIT — see [`LICENSE`](./LICENSE).
