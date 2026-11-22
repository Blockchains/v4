# Sol.College lab build of Squads V4 (devnet only)

This branch (`sol-college-devnet`) is a teaching fork. It is **not** the official Squads deployment and must never be used on mainnet.

## What changed from upstream (`3a39735`)

| File | Change | Why |
|---|---|---|
| `programs/squads_multisig_program/src/lib.rs` | `declare_id!` → `TZNQuhC8PSqQY7Szj9RJsMQirVooYSahh396QNqTMGU` | Independent lab program; the official `SQDS4ep6…` id is not reused |
| `programs/squads_multisig_program/src/instructions/program_config_init.rs` | `INITIALIZER` → `67wLf7a7oGXWV7xjBqkC4r6M7M8wjhBokArSbUaTMpWh` | Freshly generated lab key; the Squads team key and the repo test keys are not used |

No program logic was changed.

## Lab deployment (Solana devnet)

- Program: `TZNQuhC8PSqQY7Szj9RJsMQirVooYSahh396QNqTMGU` (upgrade authority `HQzo4iPSFbfDcEoFR7ogt4jKSiL13THB2xQpn8AEqXeF`, freshly generated)
- Program config: `3jcvuSo5UUrNWPig2as9hxxZs4yThAkW4QvKmfhdLTqt` (creation fee 0, lab treasury `3oRWARDAUmp2GEKDTNkAeLGkS2cUyFVESZzqA7SaEyim`)
- Client: the lab build of `Squads-Protocol/public-v4-client`, hosted at https://sol.college/dapps/squads-v4/

## Pinned build

```
cargo-build-sbf 4.1.0 --tools-version v1.52   # platform-tools v1.52, rustc 1.89; Anchor 0.32.1 crates from Cargo.lock
cargo-build-sbf --tools-version v1.52 --manifest-path programs/squads_multisig_program/Cargo.toml
```

## Initialise, operate, test, reset

`sol-college/squads-v4.mjs` (run with `@sqds/multisig@2.1.4` and `@solana/web3.js` installed) does, on devnet:

1. One-time `program_config_init` with the lab initializer (and proves a non-initializer is rejected).
2. Creates a fresh multisig (members A, B; threshold 1), then a config transaction adds member C and raises the threshold to 2.
3. Funds the **vault PDA** (never the multisig account) and checks the multisig account balance did not change.
4. Threshold transfer: outsider create/approve/execute are rejected (`NotAMember`), executing with 1 of 2 approvals is rejected (`InvalidProposalStatus`), double approval is rejected (`AlreadyApproved`), 2 of 2 executes, balances move by exactly the proposed amount, and re-execution is rejected.
5. A rejected proposal and a cancelled (previously approved) proposal cannot execute; the vault is unchanged.
6. Reads back the proposal history (`Executed, Executed, Rejected, Cancelled`).

Reset: every run uses a new random `createKey`, so it starts a new, empty multisig. Nothing is shared between runs except the program config.
