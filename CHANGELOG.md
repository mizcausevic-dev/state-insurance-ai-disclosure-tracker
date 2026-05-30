# Changelog

## [0.1] — 2026-05-29

### Added

- Initial schema with 9-state lifecycle taxonomy + 6-vehicle regulatory_vehicle taxonomy.
- 8-decision covered_decisions taxonomy + 10-actor covered_actors taxonomy + 14-doctrine obligation_kinds.
- Conditional schema requirements (effective_date / sunset_date / supersedes_event_id).
- Node verifier (`src/verify.mjs`) — schema (exit 1) / state-machine (exit 2) / supersedes-reference (exit 3) gates.
- Seed coverage for US-CT (Notice 2024-1), US-NY (DFS CL 7), US-CO (3 CCR 702-10), US-CA (Bulletin 2022-5), US-WA (TAA 2024-04).
- CI workflow.

### Not yet

- Remaining states (IL, NH, NV, OK, RI, VT + more) added per PR.
- Effective-date-as-of query helper.
- Cross-state obligation_kinds diff.
