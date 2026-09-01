# TROA Econ+ Roadmap

Every phase is implemented through chat commands, XML configuration, and the server-side plugin API. Econ+ will not add a custom UI.

## Current status

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

### Phase 2 — Hangar+ integration

- [x] Stable Hangar+ escrow integration.
- [x] Per-plugin capability discovery and semantic API capability reporting.
- [x] Isolated consumer contract checks for duplicate hold, capture, release, conflicting references, competing settlement claims, and restart retry.

### Phase 3 — Treasury policy

- [x] Treasury accounts, configurable taxes, fees, sinks, and exemptions.

### Phase 4 — Operations and reconciliation

- [ ] Statements, search, staff adjustments, reconciliation, and audit exports.

### Phase 5 — Scheduled economy

- [ ] Faction payroll, rewards, bounties, contracts, deposits, and scheduled jobs.

### Phase 6 — Risk controls

- [ ] Plugin permission policies, velocity limits, anomaly flags, and reputation signals.

### Phase 7 — Nexus v3

- [ ] Nexus v3 discovery, single-writer cross-server authority, locks, replay protection, and reconciliation.

### Phase 8 — Analytics

- [ ] Economy supply/sink reporting, transaction analytics, server health metrics, and tuning tools.

### Phase 9 — Optional credit products

- [ ] Loans and interest only after escrow, reconciliation, and recovery are production-proven.
