# Product

## What it is
- An ETL pipeline over ranked League of Legends matches of Challenger and Grandmaster players, from the official Riot API [confirmed]
- It feeds a champion comparator to study game matchups [confirmed]
- One champion: average stats (win rate, KDA and similar) [confirmed]
- Two champions, independent mode: each champion's averages side by side [confirmed]
- Two champions, head-to-head mode: only matches where the two faced each other [confirmed]
- Every answer shows its sample size and a low-confidence warning when the sample is small [confirmed]
- Run as a simulated company project: a lead asks for a full data ecosystem built from one source [confirmed]
- Public portfolio project: the repo and its process are meant to be read by others [confirmed]

- Second source: the project's own Claude Code telemetry (OpenTelemetry), processed by the same pipeline into AI delivery metrics (cost and tokens per epic and story) [confirmed]

## Who consumes it
- The project owner, studying matchups in the local Streamlit dashboard [confirmed]
- Readers of the public repo see code, docs and synthetic fixtures only, never Riot data [confirmed]

## What it is not
- Not a public stats website: the dashboard runs locally only [confirmed]
- Not a data publication: no Riot data, raw or derived, leaves the owner's machine [confirmed]
- Not a real-time system: ingestion is semi-manual because the Riot development key expires every 24 hours [confirmed]
- Not tied to one region: platform, queue, tiers and time window are pipeline parameters [confirmed]

## Scope parameters
- Platform (BR1, KR, NA1, ...) is a run parameter; the code maps each platform to its regional routing [confirmed]
- Queue, tiers and time window are run parameters [confirmed]
- Long-running extractions are acceptable; the Riot rate limit does not constrain the product [confirmed]
- Default values of these parameters [unclear: decided in E06/E07 specs · project owner]

## Data domains and owners
- All domains (players, matches, champion stats, matchups, AI delivery metrics) → project owner, gustavofs-git [confirmed]

## Freshness and service levels
- Freshness target [unclear: is there one, or is every run on demand? · project owner]
- Sample size below which a result is "low confidence" [unclear: decided in E10 spec · project owner]

## Changes
- 2026-10-22 — first version — /orca-init
- 2026-10-22 — epics renumbered after E04 Data quality framework was added; unconfirmed lines set back to [unclear] — owner review
- 2026-10-22 — Claude Code telemetry added as a second source (E00, E15) — owner decision
