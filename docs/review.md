# Review rules

## Critical in this repo
- duplicates the grain of a table
- loses rows silently
- exposes personal data (in a table, a log or a fixture)
- breaks the contract of a consumer
- writes outside its environment
- adds cost without a limit (an unbounded scan or backfill)
- commits Riot data of any kind: raw, silver, gold, logs, notebooks, fixtures, screenshots
- a player identifier (puuid, summonerId, accountId, summoner name, Riot ID) anywhere in the diff, raw or hashed
- a player identifier written to disk or logged before HMAC anonymization
- the HMAC key or the Riot API key in code, config, logs or test output
- a file under `data/`, or a path that does not start from `DATA_ROOT`
- a data source other than the official Riot API
- a dependency, service or tier that costs money or needs a credit card
- a CI job that calls the Riot API or reads prod data
- telemetry files, account identifiers (e-mail, account or organization id) or prompt content in the diff
- telemetry config that turns on prompt export

## Paths to ignore
- `target/`, `dbt_packages/`, `logs/` (dbt output)
- `.venv/`

## Warnings
- The repository is public: everything pushed, including PR text and CI logs, is published [detected: gh repo view]
- The Riot development key expires every 24 hours; a failing prod run may just be an expired key [confirmed]

## Rules from incidents

## Changes
- 2026-10-22 — first version — /orca-init
- 2026-10-22 — telemetry rules added (E00) — owner decision
