# TROA Econ+ Changelog

## v0.1.2-alpha - Staff-Verified Recovery Decisions

- Adds append-only recovery decision records to the durable ledger.
- Adds `!econadmin recovery finalize <id> <revision> <Completed|Refunded|Failed> "<audit note>"`.
- Requires native-balance verification, a current revision, and a 10–500 character audit note.
- Rejects stale, duplicate, non-recovery, and unsupported finalization attempts.
- Records the administrator, prior state/detail, final state, UTC timestamp, and note.
- Never moves credits as part of recovery finalization.

## v0.1.1-alpha - Durable Checkpoints and Restart Recovery

- Adds durable attempt and confirmation checkpoints around every native banking movement.
- Prevents concurrent retries from starting a second payer debit.
- Rejects conflicting reuse of a plugin idempotency reference.
- Makes compatible repeated capture/release requests return the existing result without moving credits twice.
- Conservatively classifies interrupted operations for staff recovery after restart.
- Adds `!econadmin recovery inspect <transaction-id-or-unique-prefix>`.
- Preserves unreadable ledgers and stops economy startup instead of silently creating an empty ledger.
- Packages the example configuration with the plugin DLL and manifest.

## v0.1.0-alpha - Initial Foundation

- Adds the .NET Framework 4.8 Torch plugin foundation.
- Uses native Space Engineers credits as the authority.
- Adds balance, payment, and transaction-history commands.
- Adds transfer limits, cooldown, fees, and treasury routing.
- Adds a durable ledger and explicit recovery states.
- Adds the versioned `IEconPlusApi` contract.
- Adds idempotent escrow hold, capture, release, and lookup operations.
- Adds administrator status, reload, and recovery inspection.
- Establishes a strict no-client/no-custom-UI architecture.
