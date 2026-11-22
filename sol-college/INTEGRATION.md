# Sol.College campus integration branch (campus-only)

This directory exists only on the `sol-college` branch of `Blockchains/v4`. It is never part of an upstream pull request.

| Role | Branch | Rule |
|---|---|---|
| Upstream-tracking baseline | `main` | Mirrors `Squads-Protocol/v4@main`. Synced with `gh repo sync Blockchains/v4 -b main`. No campus commits, never force-pushed. |
| Contribution branches | `fix/<issue-or-topic>` | Cut from the baseline. One upstream-ready change each. Source of the upstream PR. |
| Campus integration | `sol-college` | Baseline + merged contribution branches + this `sol-college/` directory. Protected (PR + 1 review, no force-push). |

## Integrated contribution branches

| Branch | Upstream PR | Status |
|---|---|---|
| `fix/proposal-create-rent-payer` | PR pending | open upstream |

Live status: `contribution_traceability_register.csv` (updated daily by the PR monitor).

## Campus configuration

- `sol-college` starts from `sol-college-devnet` (lab program id TZNQuhC8…, lab config initializer; see `sol-college/README.md`), then merges contribution branches.
- Campus checks: local validator with the `--features testing` build at GyhGAqjo…, `npx mocha … tests/index.ts`; devnet flow `sol-college/squads-v4.mjs`.

- No deployment secrets live in this fork; GitHub Actions are disabled on the fork.
- Devnet or local validator only. Never mainnet administrative identities.

## Rebuild after an upstream merge

1. `gh repo sync Blockchains/v4 -b main`
2. For each contribution branch: if the monitor reports it merged (upstream usually squash-merges, so detect by PR state, not by commit SHA), drop it; otherwise keep it.
3. Recreate `sol-college` from the new `main` head, merge the remaining contribution branches with `--no-ff`, and restore this directory from the previous `sol-college`.
4. Re-run the campus checks listed above before the branch-protection PR is approved.
