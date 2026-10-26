# 0001 — Separate environments by runtime config and promote to prod by release tag
Status: accepted
Date: 2026-10-22 · Deciders: project owner (gustavofs-git) · Reversibility: cheap

## Context
- Three environments: dev (owner's machine), ci (GitHub Actions), prod (local Docker Compose against the real Riot API).
- dev and prod run on the same machine; prod is not a remote place a pipeline can push to.
- GitHub-hosted runners cannot reach the owner's machine.
- The repository is public, and the prod machine holds the Riot API key and the HMAC key.
- One developer: GitHub does not let a PR's author approve it.
- The owner knows the Azure DevOps flow build → dev deploy → approval → prd deploy, and wants a professional process, simulated where real separation is impossible.

## Options considered
1. **Branch per environment (`dev`, `prd`).** Merge to `dev` deploys dev; a `dev` → `prd` PR deploys prod.
   Pro: familiar from Azure DevOps. Con: no separate infrastructure to deploy to; branches drift; promotion PRs carry no new code.
2. **Trunk-based, prod runs release tags; the owner pulls (chosen).** PRs merge to `main` after CI; a semver tag marks a release; prod runs only a tagged version.
   Pro: one code line, named versions, easy rollback, no access to the machine from GitHub. Con: the last step (start prod) is manual.
3. **Self-hosted GitHub runner on the owner's machine.** A full push deploy with an approval gate, like Azure DevOps.
   Pro: complete pipeline. Con: GitHub advises against self-hosted runners on public repos; a fork PR could run code on the machine holding the keys.
4. **Do nothing.** Prod is whatever is checked out. Con: no record of what ran in prod; no rollback point.

## Decision
The environment is chosen by runtime configuration (`ENV`, `DATA_ROOT`, dbt target), never by branch. `main` is the only long-lived branch; prod runs only a semver tag, checked out and started by the owner.

## Consequences
- Easier: one branch, linear history, rollback by checking out the previous tag.
- Harder: nothing enforces the manual prod step; the runbook (E14) must make it a single command.
- An approval gate (GitHub Environment) and published Docker images are left out for now; they can be added in E14 without changing this decision.

## Data impact
- No tables affected. Prod data stays under the prod `DATA_ROOT` on the owner's machine.
- Reverse: switch to option 1 or 3 with a new ADR; no data migration.

## Revisit when
- The repository becomes private (option 3 becomes safe), or
- prod moves off the owner's machine, or
- a second developer joins (real approvals become possible).
