# Roadmap

Initiative: **lol-stats v1** (GitHub Project #2). Epic issue numbers in parentheses. Epics, grouped in phases. Each epic holds 2–5 stories; stories are refined with `/orca-brief`.
Each layer epic (E07, E08, E10, E11) includes one story for that layer's quality checks.

| Phase | Item | Outcome | Depends on |
|---|---|---|---|
| 0 Telemetry | E00 (#1) AI telemetry collection | Claude Code exports its OpenTelemetry metrics and events to a local collector, which writes them as files under `DATA_ROOT`; measurement starts before any other epic | — |
| 1 Foundation | E01 (#2) Dev environment | Anyone can clone the repo and run the tests in minutes | — |
| 1 Foundation | E02 (#3) CI guardrails | No PR can break the non-negotiable rules (data/, secrets, player ids) | E01 |
| 1 Foundation | E03 (#4) Shared utilities | One logger, one config loader, one HMAC anonymizer for every job | E01 |
| 1 Foundation | E04 (#5) Data quality framework | Every run records its metadata, checks have a severity (error blocks the next layer, warn records), invalid payloads go to quarantine, each run has a quality report | E03 |
| 2 Bronze → Silver | E05 (#6) Ingestion framework | A Riot API client (limits, retries, platform → region routing) and the generic `source_to_bronze` loader; code-design ADR | E03, E04 |
| 2 Bronze → Silver | E06 (#7) Player pool | Anonymized Challenger/Grandmaster players per platform, in bronze | E05 |
| 2 Bronze → Silver | E07 (#8) Match ingestion | Those players' ranked matches in bronze, without duplicates, resumable, with bronze checks | E06 |
| 2 Bronze → Silver | E08 (#9) Silver | Clean match and participant tables, with silver checks | E07 |
| 3 Orchestration I | E09 (#10) Airflow: ingest → silver | Airflow in Compose runs ingestion → quality → silver | E08 |
| 4 Gold & dashboard | E10 (#11) Champion stats | Average stats per champion, with sample size, low-confidence flag and gold checks (dbt) | E08 |
| 4 Gold & dashboard | E11 (#12) Matchups | Two champions side by side and head-to-head, with gold checks (dbt) | E10 |
| 4 Gold & dashboard | E12 (#13) Dashboard | Streamlit comparator (one- and two-champion views) and a data health page; synthetic by default | E10 (E11 for the two-champion view) |
| 5 Orchestration II | E13 (#14) Airflow: gold | The DAG extends to silver → quality → dbt run/test | E09, E11 |
| 6 Release & ops | E14 (#15) Release & ops | Release tags with CHANGELOG, prod runbook, release v1.0 | E13 |
| 7 AI delivery metrics | E15 (#16) AI delivery metrics | The telemetry from E00 flows bronze → silver → gold with the same stack; a dashboard page shows AI cost and tokens per epic and per story | E00, E13, E12 |

## Why this order
Telemetry collection comes first (E00) because it only measures what happens after it is on.
Shared utilities and the quality framework come next (E01–E04), so no feature builds its own logger, anonymizer or checks.
Data then moves layer by layer, and each layer is orchestrated as soon as it exists (E09 after silver, E13 after gold), so orchestration grows with the pipeline instead of arriving at the end.

## Changes
- 2026-10-22 — first version — /orca-init
- 2026-10-22 — E04 Data quality framework added, later epics renumbered, checks story per layer, data health page — owner review
- 2026-10-22 — E00 AI telemetry collection and E15 AI delivery metrics added; telemetry reuses the project stack instead of Prometheus/Grafana — owner decision
- 2026-10-22 — epic issues created (#1–#16) in Project #2 — owner approval
