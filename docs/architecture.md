# Architecture

## Stack
- Language for ingestion and bronze/silver: Python [confirmed]
- Python version [unclear: set in E01 · project owner]
- Package manager [unclear: set in E01 · project owner]
- Engine: DuckDB [confirmed]
- Storage format for silver and gold: Parquet [confirmed]
- Transformation silver → gold: dbt with the dbt-duckdb adapter [confirmed]
- Orchestrator: Apache Airflow [confirmed]
- Airflow version [unclear: set in E09 · project owner]
- Dashboard: Streamlit, reading gold through DuckDB [confirmed]
- Runtime: Docker Compose with Airflow, the pipeline app and the dashboard [confirmed]
- CI: GitHub Actions [confirmed]

- AI telemetry: Claude Code OpenTelemetry export → an OpenTelemetry Collector container writing files under `DATA_ROOT` [confirmed]
- No Prometheus or Grafana: telemetry is processed and shown with the project's own stack (DuckDB, dbt, Streamlit) [confirmed]
- Telemetry environment variables, metric names and how a session is tagged with its work item [unclear: checked against the official Claude Code docs in E00 spec · project owner]

## Stack profile
- profile: python-duckdb (ingestion, bronze, silver) [confirmed]
- profile: dbt (silver → gold) [confirmed]
- Commands in both profiles are `[unclear]` until E01 sets them [confirmed]

## Repos
- One repository, `lol-stats`, holds ingestion, transformation, orchestration, dashboard and docs [confirmed]
- The repository is public: anything pushed is published [detected: gh repo view]

## Layers
- Medallion architecture: bronze, silver, gold [confirmed]
- **Bronze**: raw JSON responses from the Riot API, player identifiers already replaced by HMAC at ingestion [confirmed]
- Bronze never holds a raw player identifier, not even briefly on disk [confirmed]
- **Silver**: clean, typed tables in Parquet, queried with DuckDB, validated [confirmed]
- Who builds silver from bronze (Python or dbt) [unclear: decided in E08 spec, with an ADR · project owner]
- **Gold**: Parquet tables with the comparator's metrics, built by dbt and tested with dbt tests [confirmed]
- Every gold metric carries its sample size [confirmed]
- All layer paths start from `DATA_ROOT`; no path is hard-coded [confirmed]

## Environments
- Three environments: dev, ci, prod [confirmed]
- The environment is chosen by runtime configuration (`ENV`, `DATA_ROOT`, dbt target), never by branch [confirmed]
- **dev**: the owner's machine, synthetic fixtures by default [confirmed]
- **ci**: GitHub Actions, synthetic fixtures only, never the Riot API [confirmed]
- **prod**: the local Docker Compose stack against the real Riot API, with the real `DATA_ROOT` [confirmed]
- prod is production; only the owner starts it; agents never run prod commands [confirmed]
- Isolation: each environment has its own `DATA_ROOT`; dev and ci never read prod data [confirmed]
- Variable names other than `ENV` and `DATA_ROOT` [unclear: set in E01 · project owner]

## Deploy pipeline
- Decision record: docs/adr/0001-promote-by-release-tag.md [confirmed]
- On every PR to `main`: CI runs lint, tests and dbt build on synthetic fixtures [confirmed]
- CI fails if any file exists under `data/` [confirmed]
- Merge to `main` means validated, not deployed [confirmed]
- A release is a semver tag `vX.Y.Z` on `main`; GitHub builds its release notes [confirmed]
- prod runs only a tagged version: the owner checks out the tag and starts Compose with `ENV=prod` [confirmed]
- No GitHub job reaches the owner's machine; no self-hosted runner [confirmed]
- Approval gate before release and published Docker images: not used for now; candidates for E14 [confirmed]
- Workflow files and job names [unclear: set in E02 and E14 · project owner]

## Commands
- test (focused): [unclear: set in E01 · project owner]
- test (suite): [unclear: set in E01 · project owner]
- lint: [unclear: set in E01 · project owner]
- build: [unclear: set in E01 · project owner]
- dbt build (dev): [unclear: set in E10 · project owner]
- docs from contracts: [unclear: set in E10 · project owner]
- run prod: [unclear: set in E14 · project owner]

## Security and governance
- Riot data never goes to Git: raw, silver, gold, logs, notebooks, fixtures or screenshots [confirmed]
- No player identifier in the repo: puuid, summonerId, accountId, summoner name, Riot ID [confirmed]
- Player identifiers are replaced by an HMAC with a secret key at ingestion, before the first write [confirmed]
- The HMAC key never enters the repo [confirmed]
- The Riot API key lives only in an environment variable or a CI secret [confirmed]
- The development key expires every 24 hours; the owner renews it by hand [confirmed]
- Only official Riot sources (developer.riotgames.com); no unofficial MCP or third-party data [confirmed]
- `data/` is in `.gitignore`; every path starts from `DATA_ROOT` [confirmed]
- Publishing any derived Riot data needs the owner's explicit decision first [confirmed]
- Claude Code telemetry carries account attributes; its files stay under `DATA_ROOT` and never go to Git [confirmed]
- Prompt content is not exported in telemetry [confirmed]
- Posts show only aggregated telemetry numbers, without account identifiers [confirmed]
- Retention of bronze data [unclear: how long is raw JSON kept? · project owner]

## Operations
- Cost: zero, free tier only, no credit card; anything that seems to need one stops the work and asks [confirmed]
- On-call and incidents [unclear: does the owner handle every failure by hand? · project owner]
- Prod runbook [unclear: written in E14 · project owner]

## Changes
- 2026-10-22 — first version — /orca-init
- 2026-10-22 — epics renumbered after E04 Data quality framework was added; unconfirmed lines set back to [unclear] — owner review
- 2026-10-22 — Claude Code telemetry added as a second source (E00, E15) — owner decision
