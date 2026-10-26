# Testing

## Test levels here
| Level | Proves | Here | Command |
|---|---|---|---|
| Pure transformation | logic over a few rows | [unclear: set in E01 and E10 · project owner] | [unclear: set in E01 and E10 · project owner] |
| Component | a job or model against a local engine | on synthetic fixtures [confirmed]; form [unclear: set in E01 and E10 · project owner] | [unclear: set in E01 and E10 · project owner] |
| Contract | the output schema did not change silently | [unclear: E08 and E10 specs · project owner] | [unclear: set in E08 and E10 · project owner] |
| Integration | works on the real source | a small prod run by the owner; never in CI [confirmed] | [unclear: set in E14 · project owner] |
- No tests exist yet [detected: empty repo]
- Test framework for Python [unclear: set in E01 · project owner]

## Test data
- fixture policy: synthetic-only (also in .claude/orca/config.json) [confirmed]
- Fixtures are written by hand or generated; never copied, trimmed or "anonymized" from a real response [confirmed]
- Fixtures never contain a real puuid, summonerId, accountId, summoner name or Riot ID, not even hashed [confirmed]
- The dashboard runs on synthetic fixtures by default [confirmed]
- Fixture location [unclear: set in E01 · project owner]

## Local and CI
- Local: the whole suite runs on synthetic fixtures without a Riot API key [confirmed]
- CI on every PR to `main`: lint, tests, dbt build on fixtures [confirmed]
- CI fails if any file exists under `data/` [confirmed]
- CI never calls the Riot API and holds no Riot key [confirmed]
- CI scans for secrets and player identifiers [confirmed]
- Scanning tool [unclear: set in E02 · project owner]

## Runtime checks
- A data quality framework is built once, in E04, before the first load [confirmed]
- Severity `error`: the next layer does not run (write-audit-publish) [confirmed]
- Severity `warn`: the run continues and the result is recorded [confirmed]
- Invalid payloads go to quarantine, never silently dropped [confirmed]
- Every run records its metadata: run id, platform, window, rows read, written and quarantined, duration, check results [confirmed]
- Every run produces a quality report [confirmed]
- Each layer epic (bronze, silver, gold) has its own checks story [confirmed]
- Gold: dbt data tests [confirmed]
- Bronze and silver: checks run before data moves to the next layer [confirmed]
- The Airflow DAG has an explicit quality step between layers [confirmed]
- The dashboard has a data health page: last run, freshness, quarantined counts [confirmed]
- Bronze/silver check tool and report format [unclear: set in E04 spec · project owner]
- Alert destination [unclear: run log only, or a channel? · project owner]

## Changes
- 2026-10-22 — first version — /orca-init
- 2026-10-22 — data quality approach added; unconfirmed test forms set back to [unclear] — owner review
