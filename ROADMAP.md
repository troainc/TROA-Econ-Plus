# TROA Econ+ Roadmap

Every phase is implemented through chat commands, XML configuration, and the server-side plugin API. Econ+ will not add a custom UI.

## Current status

- Current build: `v0.9.0-alpha`
- Build validation: .NET Framework 4.8 x64 build completed with zero warnings and zero errors.
- Package: `TROA-Econ-Plus-v0.9.0-alpha.zip`
- Architecture: Torch server plugin using commands, XML configuration, and the server-side API only; no custom UI or client mod.
- Implemented scope: Phases 1–9 are implemented in source. Production validation remains required before the alpha designation can be removed.

### Phase 1 — Safe transaction foundation (in progress)

- [x] Explicit `Prepared -> FundsHeld -> Completed/Refunded/RecoveryRequired` transaction states.
- [x] Atomic XML ledger replacement with a retained backup.
- [x] Durable checkpoints before payer debit, payee credit, treasury movement, reversal, and refund attempts.
- [x] Strict `sourcePlugin + externalReference` idempotency with payload-conflict rejection.
- [x] Single-claim protection so concurrent retries cannot start a second debit.
- [x] Player balance, transfer, history, limits, fee, cooldown, and permission enforcement.
- [x] Conservative startup classification for interrupted transactions.
- [x] Recovery queue plus staff checkpoint inspection.
- [x] Corrupt-ledger preservation with fail-closed economy startup.
- [x] Automated ledger checks for duplicate references, conflicting reuse, debit claims, restart recovery, and corruption.
- [ ] Live-server validation against native Space Engineers banking failure paths.
- [x] Staff-reviewed recovery finalization entries after native balances have been independently verified.
- [x] Optional Discord webhook audit delivery for completed, refunded, failed, and recovery-required results.

### Phase 2 — Hangar+ integration (implemented)

- [x] Stable Hangar+ escrow integration.
- [x] Per-plugin capability discovery and semantic API capability reporting.
- [x] Isolated consumer contract checks for duplicate hold, capture, release, conflicting references, competing settlement claims, and restart retry.

### Phase 3 — Treasury policy (implemented)

- [x] Treasury accounts, configurable taxes, fees, sinks, and exemptions.

### Phase 4 — Operations and reconciliation (implemented)

- [x] Player and staff statements with bounded results.
- [x] Transaction search by ID, external reference, purpose, and detail.
- [x] Reason-required, treasury-funded positive staff adjustments.
- [x] Durable reconciliation summaries that do not silently change transaction state.
- [x] Server-local UTF-8 CSV audit exports.

### Phase 5 — Scheduled economy (implemented)

- [x] Faction-treasury payroll, rewards, bounties, contracts, and deposits.
- [x] One-time and repeating scheduled programs.
- [x] Automatic game-thread execution with bounded work per pass.
- [x] Durable per-run idempotency references and recovery-required pausing.

### Phase 6 — Risk controls (implemented)

- [x] API-plugin allowlist policies.
- [x] Rolling transaction-count and credit-volume limits.
- [x] Daily payer transfer limits.
- [x] Durable non-punitive anomaly flags.
- [x] Informational reputation signals for successful, failed, and recovery-required outcomes.

### Phase 7 — Nexus v3 safeguards (implemented; transport pending)

- [x] Unique local-server and designated-authority configuration.
- [x] Single-writer authority enforcement.
- [x] Durable short player leases.
- [x] Replay IDs and payload-conflict rejection.
- [x] Reconciliation safeguards and administrator status reporting.
- [ ] Integrate and live-test an approved authenticated Nexus v3 transport adapter.

### Phase 8 — Analytics (implemented)

- [x] Completed volume, fees, taxes, refunds, held funds, and transaction-state totals.
- [x] Unique-player and configured UTC-window reporting.
- [x] Server-local audit exports for external analysis.
- [x] Read-only analytics that never alter authoritative balances.

### Phase 9 — Optional credit products (implemented; disabled by default)

- [x] Treasury-funded administrator-approved loans.
- [x] Configurable principal, APR, term, payment interval, and active-loan limits.
- [x] Explicit whole-day interest accrual.
- [x] Native borrower-debit and treasury-credit repayment checkpoints.
- [x] Loan recovery and default states without automatic collections or collateral seizure.

## Production validation gates

- [ ] Validate successful, insufficient-funds, failed-credit, refund, and treasury-reversal paths on a disposable live Space Engineers server.
- [ ] Restart the server at each transaction checkpoint and verify conservative recovery classification.
- [ ] Validate Hangar+ duplicate hold, capture, release, and restart retries against the packaged plugin.
- [ ] Validate scheduled payroll and loan processing across restarts and configuration reloads.
- [ ] Load-test velocity limits, analytics, exports, and ledger growth with production-sized data.
- [ ] Validate Nexus only after an authenticated transport adapter is selected and reviewed.
- [ ] Promote from alpha only after backup, rollback, and recovery procedures are documented and tested by server staff.

## Future hardening

- Append-only ledger segments, integrity hashes, indexed statements, and compact snapshots for larger servers.
- Configurable anomaly acknowledgement and staff notes.
- Prometheus-compatible economy metrics without exposing private player balances.
- Approved-plugin circuit breakers and per-consumer limits.
- Loan approval workflows and collateral references only after the current funded-loan path is production-proven.
