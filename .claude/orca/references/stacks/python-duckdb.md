# Stack profile: Python + DuckDB (ingestion, bronze, silver)

Loaded after `docs/architecture.md` names `python-duckdb` as a profile. Gold (dbt-duckdb) uses the shipped `dbt` profile. The commands in `docs/architecture.md` always win.

Engine: DuckDB [confirmed]
Python and DuckDB versions: [unclear: set in E01 · project owner]
Table format: Parquet files under `DATA_ROOT` (bronze stays raw JSON) [confirmed]
Build tool / package manager: [unclear: set in E01 · project owner]

## Commands
| Purpose | Command |
|---|---|
| focused tests | [unclear: set in E01 · project owner] |
| one test | [unclear: set in E01 · project owner] |
| suite | [unclear: set in E01 · project owner] |
| lint and format | [unclear: set in E01 · project owner] |
| build / package | [unclear: set in E01 · project owner] |
| run a job in dev | [unclear: set in E05 · project owner]; always with `ENV=dev` and a dev `DATA_ROOT` |
| consumers (lineage) | search the repo for the table name; read the Airflow DAG (from E09); `dbt ls --select <source>+` for gold readers [confirmed] |
| docs from contracts | [unclear: set in E08 · project owner] |

Never a command that matches `production.command_patterns` in `.claude/orca/config.json`.
Never a command that calls the Riot API outside `ENV=prod` started by the owner.

## Contract
- Ingestion code is object-oriented: a generic `source_to_bronze` loader plus one config per endpoint [confirmed]
- Silver contract form [unclear: chosen in E08 spec; options include a schema file next to the job plus a contract test, or typed models validated at write · project owner]

## Tests
| Level | Form in this stack |
|---|---|
| pure transformation | [unclear: set in E01 · project owner] |
| component | [unclear: set in E01 · project owner] |
| contract | [unclear: E08 spec · project owner] |
| integration | a small prod run by the owner; never in CI [confirmed] |
| runtime checks | the data quality framework from E04, before each layer write [confirmed] |

- Tests never call the Riot API; the HTTP layer is replaced by synthetic responses [confirmed]
- Test framework and the agreed test shape [unclear: set in E01 · project owner]

## Fixtures
- Synthetic only, written by hand or generated; never derived from a real response [confirmed]
- Fake player identifier format [unclear: set in E01 · project owner]
- Location and format [unclear: set in E01 · project owner]

## Floor guard patterns
Flag in a diff, unless the spec names the change:
- a validation removed, skipped or turned into a warning;
- a broad `try/except` that swallows a failed payload instead of quarantining it;
- a dedup (`DISTINCT`, `drop_duplicates`, `QUALIFY row_number() = 1`) added to hide a grain bug;
- a default or `COALESCE` over a column that was failing a check;
- a filter that silently drops rows that fail validation;
- a test skipped, marked xfail or deleted;
- the HMAC step moved after a write, or made optional;
- a hard-coded path instead of one built from `DATA_ROOT`.
