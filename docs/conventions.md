# Conventions

## Language
- Docs, code, tables and columns in English [confirmed]
- In doubt about scope, Riot terms or data publication: ask before acting [confirmed]

## Code organization
- Ingestion is object-oriented: one generic loader (`source_to_bronze`) and one configuration per endpoint [confirmed]
- The generic loader is built before the first concrete endpoint, because the roadmap already shows several endpoints of the same API with the same shape [confirmed]
- Endpoints that differ (pagination, one call per id) extend the loader through hooks, not copies [confirmed]
- Class design, hooks and config format [unclear: decided in the first E05 spec, with an ADR · project owner]
- Bronze → silver transformations: generic where tables are flat, specific where payloads nest [confirmed]
- Shared utilities (logger, config, HMAC anonymizer) live in one module and are built in E03, before their users [confirmed]
- Folder layout [unclear: set in E01 · project owner]

## Naming
- Table, model, file and column naming [unclear: set in E08 (silver) and E10 (gold) specs · project owner]

## Contracts
- Gold contract form, for example the dbt model YAML with `contract: enforced` [unclear: chosen in E10 spec · project owner]
- dbt contracts apply to table, view and incremental models; gold as Parquet files may need dbt-duckdb's external materialization, which may not support them [unclear: checked in E10 spec against dbt-duckdb docs · project owner]
- Silver and bronze contract form [unclear: chosen in E08 spec · project owner]

## Git
- integration branch: `main`, protected [confirmed]
- branch name: `<type>/<issue>-<slug>`, for example `feat/12-match-ingestion` [confirmed]
- commit message: Conventional Commits [confirmed]
- merge: squash, PR title in Conventional Commits, branch deleted after merge [confirmed]
- releases: semver tags `vX.Y.Z` on `main` (see docs/adr/0001-promote-by-release-tag.md) [confirmed]

## Pull requests
- Agents open PRs as draft; `/orca-qa` marks them ready after its review [confirmed]
- The owner reads the PR on GitHub as the final record of the change [confirmed]
- Human approval = the owner merges the PR on GitHub; agents never merge [confirmed]
- `main` protection: PR required, CI green required, 0 required approvals (GitHub blocks self-approval) [confirmed]
- The owner reviews every PR [confirmed]
- CODEOWNERS file [unclear: needed with a single reviewer? · project owner]

## Tracker
- GitHub Issues with one GitHub Project per initiative; first initiative: "lol-stats v1" [confirmed]
- Hierarchy: initiative = Project; epic = issue labelled `epic`; story = sub-issue of an epic, labelled `story` [confirmed]
- A story is the Orcastrator work item (brief → discover → spec → plan → build → qa → ship) [confirmed]
- Every story is split by `/orca-plan` into tasks T1..Tn: one task = one PR [confirmed]
- Each task is mirrored as a sub-issue of its story, labelled `task` (`mirror_tasks: true`) [confirmed]
- Does the task's PR close its task issue with `Closes #<task>` in the PR body? [unclear: proposed, not yet confirmed · project owner]
- Story state is a label: `status:brief`, `status:discover`, `status:spec`, `status:plan`, `status:build`, `status:qa`, `status:done` [confirmed]
- Epics are grouped in phases (see docs/roadmap.md) [confirmed]

## Platform commands
| Operation | Command |
|---|---|
| view PR (title, body, source and target branch, draft) | `gh pr view <PR> --json title,body,headRefName,baseRefName,isDraft` [confirmed] |
| PR diff | `git fetch origin <target> <source> && git diff origin/<target>...origin/<source>` [confirmed] |
| open draft PR | `gh pr create --draft --base main --title "<title>" --body-file <file>` [confirmed] |
| read PR comments | `gh pr view <PR> --comments` [confirmed] |
| comment on PR | `gh pr comment <PR> --body-file <file>` [confirmed] |
| human approval status | manual: the owner merges on GitHub; check with `gh pr view <PR> --json state` (MERGED = approved) [confirmed] |
| mark ready for review | `gh pr ready <PR>` [confirmed] |
| merge | manual: the owner clicks "Squash and merge" on GitHub [confirmed] |
| PR state (open, merged) | `gh pr view <PR> --json state` [confirmed] |
| view work item | `gh issue view <id>` [confirmed] |
| create work item | `gh issue create --title "<title>" --body-file <file> --label <epic\|story\|task>` [confirmed] |
| add work item to the Project | `gh project item-add 2 --owner gustavofs-git --url <issue url>` (gh 2.45 `--project` fails on the retired classic Projects API) [detected: gh issue create error] |
| link child to parent (story → epic, task → story) | `gh api -X POST repos/gustavofs-git/lol-stats/issues/<parent>/sub_issues -F sub_issue_id=$(gh api repos/gustavofs-git/lol-stats/issues/<child> --jq .id)` [confirmed] |
| comment on work item | `gh issue comment <id> --body-file <file>` [confirmed] |
| move work item | `gh issue edit <id> --add-label status:<stage> --remove-label status:<previous>` [confirmed] |
| close work item | `gh issue close <id> --comment "<text>"` [confirmed] |

## Decisions
- ADRs live in `docs/adr/NNNN-<title>.md`, numbered from 0001 [confirmed]
- Every dbt-related proposal states pros and cons, compared with the Spark/Databricks equivalent [confirmed]

## Changes
- 2026-10-22 — first version — /orca-init
- 2026-10-22 — epics renumbered after E04 Data quality framework was added; unconfirmed lines set back to [unclear] — owner review
- 2026-10-22 — plan tasks mirrored as sub-issues of their story — owner decision
- 2026-10-22 — Project add command changed to gh project item-add — gh 2.45 --project hits the retired classic API
