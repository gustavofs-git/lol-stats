# lol-stats

ETL portfolio project: ranked Challenger/Grandmaster League of Legends matches from the official Riot API → medallion layers (bronze JSON, silver Parquet, gold Parquet via dbt-duckdb) → a local Streamlit champion comparator. Run as a simulated company project.

## Non-negotiable rules
1. No Riot data in Git: raw, silver, gold, logs, notebooks, fixtures or screenshots. Only code, tests, config, docs and synthetic fixtures.
2. No player identifier in the repo (puuid, summonerId, accountId, summoner name, Riot ID). Identifiers are replaced by HMAC at ingestion, before any write. The HMAC key never enters the repo.
3. Zero cost, free tier only, no credit card. If something seems to need one, stop and ask.
4. Secrets (Riot API key, HMAC key) only in environment variables or CI secrets.
5. Only official Riot sources (developer.riotgames.com). No unofficial MCP.
6. `data/` is git-ignored and CI fails if it has files. Every path starts from `DATA_ROOT`.
7. Never run anything in prod (`ENV=prod`, `--target prod`). Only the owner does.
8. The repo is public: anything pushed is published.

## Conventions
- Docs, code, tables and columns in English.
- In doubt about scope, Riot terms or data publication: ask before acting.
- Trunk-based on `main`; branches `<type>/<issue>-<slug>`; Conventional Commits; squash merge. Agents open draft PRs and never merge.
- Every dbt proposal comes with pros and cons, compared with the Spark/Databricks equivalent.

## Commands
Not set yet: E01 defines them in `docs/architecture.md` (Commands).

## Where things are documented
- `docs/product.md`: what the product is and is not
- `docs/architecture.md`: stack, layers, environments, deploy, security
- `docs/conventions.md`: code organization, git, PRs, tracker, platform commands
- `docs/testing.md`: test levels, fixtures, CI
- `docs/review.md`: what is Critical in this repo
- `docs/roadmap.md`: epics and their order
- `docs/adr/`: decisions
