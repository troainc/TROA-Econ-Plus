# TROA Econ+ Changelog

## v0.9.5-alpha - Full Banking Expansion

- Adds ledger-based account recovery preview/rebuild with explicit confirmation and pre-recovery signed backups.
- Adds Keen drift reports, expected-drift repair confirmation, and one-account Keen imports.
- Adds offline and conflict-safe exact-name payments using durable Steam ID64 aliases.
- Adds checking, savings, business, and managed faction treasury accounts.
- Routes deposits, withdrawals, and named-account payments through durable transaction checkpoints with confirmed reversals or recovery classification.
- Adds rent, subscription, tax, loan-payment, and general recurring schedules with pause/resume/cancel controls.
- Routes scheduled taxes to the Econ+ treasury and runs loan reminders/automatic payments even when general schedules are disabled.
- Adds credit scores, approval minimums, grace periods, late fees, refinancing, and automatic-payment/reminder configuration.
- Adds faction daily limits and approval thresholds as durable policy fields.
- Adds faction manager grant/revoke commands, policy updates, expiring approval requests, and confirmed approved payouts.
- Adds Compact, Detailed, Loan, Faction, and Market LCD templates.
- Adds supply, median wealth, top-decile wealth, volume, sinks, sources, and inactivity reports.
- Adds retained historical supply snapshots and calculated inflation/deflation reporting.
- Adds repeated-failure, account-funnel, and unusual authoritative balance-change fraud signals alongside existing velocity controls.
- Adds audited compensating rollback transactions without mutating original history.
- Adds persistent maintenance mode for safe backups, migration, and incident response.
- Adds SHA-256-signed backups and account exports.
- Adds guarded merge/replace CSV/XML migration from a restricted Imports folder with validation, confirmation, and automatic signed backup.
- Adds polished optional masked-account Discord banking embeds through a separate webhook while keeping detailed statements in-game by default.
- Publishes API v2 capabilities for standalone accounts, offline payments, named accounts, and maintenance guards.
- Preserves local milestone review packages separately from the official release line.

### Standalone Account Foundation Included in v0.9.5-alpha

- Makes the durable Steam ID64 Econ+ account database the authoritative balance source.
- Adds optional one-time Keen balance import and best-effort Keen balance mirroring for vanilla compatibility.
- Adds atomic `Accounts.xml` persistence with corrupt-file preservation and fail-closed startup behavior.
- Routes player transfers, treasury payouts, scheduled programs, adjustments, loans, repayments, and plugin API balances through Econ+ accounts.
- Adds player `dashboard`, `risk`, `loanapply`, and `loanrepay` commands while retaining the existing loan command path.
- Adds owned LCD account dashboards with configurable name tag, refresh interval, and recent-transaction count.
- Enables configurable player loan applications with principal and term limits.
- Adds complete configuration examples and player-facing documentation for standalone accounting.

## v0.9.0-alpha - Risk, Nexus Guards, Analytics, and Credit

- Completes roadmap phases 6–9 while retaining the command/config-only server architecture.
- Adds API-plugin allowlists, rolling count/credit velocity limits, daily payer limits, anomaly flags, and durable reputation signals.
- Enforces risk policy in native player transfers and plugin escrow creation.
- Adds Nexus-ready server identity, designated single-writer authority, player leases, durable replay IDs, payload-conflict rejection, and reconciliation visibility.
- Keeps Nexus transport disabled by default and does not claim to provide the external Nexus message adapter.
- Adds economy volume, fee, tax, refund, held-funds, state, and unique-player analytics.
- Adds optional treasury-funded loans with principal limits, terms, explicit daily interest accrual, borrower repayments, native banking checkpoints, and recovery states.
- Keeps all credit products disabled by default and performs no automatic collections or collateral seizure.
- Adds `EconPlusAdvanced.xml` for anomalies, reputation, Nexus guards, and loan state.

## v0.5.0-alpha - Operations and Scheduled Economy

- Completes roadmap phases 4 and 5 with player statements, staff transaction search, treasury-funded adjustments, reconciliation summaries, and CSV audit exports.
- Adds a separate atomically replaced operations ledger for adjustment evidence, reconciliation records, and scheduled program state.
- Adds funded payroll, reward, bounty, contract, and deposit programs with one-time or repeating schedules.
- Runs due programs automatically on the Space Engineers game thread when `EnablePayroll` is enabled; administrators can also trigger a due run safely.
- Uses a unique program revision reference for idempotent retries and pauses a program in `RecoveryRequired` after an unresolved payout.
- Requires native treasury funding by default and never mints scheduled credits silently.
- Adds player `statement` and `programs` commands plus complete phase 4/5 administrator commands.
- Fixes failed recipient-credit handling so a confirmed native failure can release its settlement claim before a safe refund or treasury restoration.

## v0.3.0-alpha - Treasury Policy

- Separates player-transfer fees and taxes in the durable transaction record.
- Adds configurable transfer taxes, combined-charge caps, Steam-ID exemptions, and reserved plugin exemptions.
- Adds `Treasury` and `Sink` charge destinations while preserving durable movement and reversal checkpoints.
- Uses checked arithmetic and deterministic upward whole-credit rounding.
- Adds `!econadmin treasury` for safe policy inspection without exposing exemption values.
- Extends isolated contract checks for fee/tax calculation, sink routing, and exemptions.

## v0.2.0-alpha - Hangar+ Escrow Contract

- Adds semantic API v1.1 capability discovery through `EconPlusApiRegistry.TryDiscover` without removing the v1 base interface.
- Reports retry-safe hold, capture, release, restart recovery, balance lookup, recovery inspection, hold limit, expiration, and game-thread requirements per consumer plugin.
- Adds a configurable `MaximumEscrowCredits` server-side limit.
- Prevents capture and release from acquiring competing settlement claims on the same held funds.
- Adds `!econadmin escrowtest`, an isolated fake-bank contract suite for duplicate hold/capture/release calls, conflicting references, restart retry, and competing settlement claims.
- The contract suite never reads or changes live Space Engineers balances.

## v0.1.3-alpha - Optional Discord Economy Audits

- Adds asynchronous Discord webhook audit embeds for completed, refunded, failed, and recovery-required results.
- Adds independent event switches and a bounded queue; failed-event notices default off and recovery alerts default on.
- Adds `!econadmin webhook` and `!econadmin webhook test`.
- Uses the configurable `DiscordWebhookName` while retaining the webhook's external server branding.
- Omits Steam IDs, external references, recovery notes, webhook URLs, and checkpoint details from embeds.
- Does not retry compatible terminal API calls into duplicate webhook messages.
- Ensures delivery failures or queue pressure never change native balances or transaction state.

## v0.1.2-alpha - Staff-Verified Recovery Decisions

- Adds append-only recovery decision records inside the durable ledger.
- Adds `!econadmin recovery finalize <id> <revision> <Completed|Refunded|Failed> "<audit note>"`.
- Requires a current transaction revision and a 10–500 character note before finalization.
- Restricts finalization to `RecoveryRequired` transactions and rejects duplicate or stale decisions.
- Records the administrator Steam ID, previous state/detail, verified final state, UTC timestamp, and note.
- Does not move credits during finalization; staff must independently verify native balances first.
- Adds automated checks for stale revisions, required notes, unique-prefix lookup, duplicate rejection, and decision persistence.

## v0.1.1-alpha - Durable Checkpoints and Restart Recovery

- Adds durable attempt and confirmation checkpoints for payer debits, payee credits, treasury movements, reversals, and refunds.
- Prevents concurrent or repeated requests from claiming a second payer debit.
- Rejects an idempotency reference when the source reuses it with different parties, amounts, fees, or purpose.
- Makes repeated escrow capture and release calls return the existing compatible terminal result without moving credits twice.
- Classifies interrupted transactions conservatively at startup and exposes the classification count to administrators.
- Adds `!econadmin recovery inspect <transaction-id-or-unique-prefix>` for staff checkpoint review.
- Preserves corrupt ledgers under a timestamped name and stops economy startup instead of silently creating an empty ledger.
- Reorders fee settlement and records treasury reversal/refund work so failed player transfers remain recoverable.
- Adds automated ledger safety checks for idempotency, debit claims, restart states, and corrupt-file preservation.

## v0.1.0-alpha - Initial Foundation

- Creates the .NET Framework 4.8 Torch plugin project.
- Adds native-economy balance and player transfer commands.
- Adds a durable transaction ledger and explicit recovery states.
- Adds configurable limits, fees, cooldowns, and treasury routing.
- Adds the versioned server-side `IEconPlusApi` contract.
- Adds idempotent escrow hold, capture, release, and transaction lookup operations for Hangar+ and other plugins.
- Adds administrator status, reload, and recovery inspection commands.
- Establishes a strict no-UI architecture.
