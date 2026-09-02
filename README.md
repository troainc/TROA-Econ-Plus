# TROA Econ+

TROA Econ+ is a server-side Torch economy plugin for Space Engineers. It provides durable accounting, escrow, treasury policy, and a versioned integration API for Hangar+ and other TROA plugins. It has no client mod, desktop UI, web UI, WPF, or WinForms dependency.

> Current release: `v0.9.0-alpha`  
> Runtime: Torch / .NET Framework 4.8 / x64  
> Interface: Space Engineers chat commands, XML configuration, and server-side plugin API only

## Installation

1. Back up the world, `TROA-Econ-Plus.cfg`, and `TROA-Econ-PlusData`.
2. Install `releases/TROA-Econ-Plus-v0.9.0-alpha.zip` through Torch.
3. Restart Torch so the updated command modules and API are loaded.
4. Review the generated configuration before enabling payroll, Nexus safeguards, or credit products.
5. Run `!econadmin status`, `!econadmin escrowtest`, and `!econadmin webhook test` where applicable.
6. Validate small transactions on a disposable development server before production use.

## Current foundation

- Native Space Engineers credit balances remain authoritative.
- Durable XML transaction ledger with explicit states and atomic saves.
- Durable checkpoints before every native debit, credit, treasury movement, reversal, and refund attempt.
- Strict idempotency matching that rejects reuse of a reference with different transaction details.
- Conservative restart recovery and fail-closed corrupt-ledger preservation.
- Player balance, payment, and transaction-history commands.
- Configurable transfer limits, cooldown, percentage fee, and treasury faction.
- Versioned `IEconPlusApi` for server-side plugin integrations.
- Idempotent external references for retry-safe consumer operations.
- Durable escrow holds with capture and release operations.
- Semantic API v1.1 capability discovery for Hangar+ and other server-side consumers.
- Mutually exclusive capture/release settlement claims so the same hold cannot be credited and refunded.
- An isolated administrator escrow contract check that never touches live player balances.
- Separate transfer fee and tax calculations with deterministic upward rounding, an optional combined-charge cap, Steam-ID exemptions, and treasury-or-sink routing.
- Administrator status, reload, and recovery-queue commands.
- Release ZIP packaging for Torch on .NET Framework 4.8.
- Personal statements plus administrator search, reconciliation, adjustments, and CSV audit exports.
- Durable faction-treasury payroll, rewards, bounties, contracts, deposits, and scheduled jobs.
- Automatic scheduled-job execution on the Space Engineers game thread with idempotent revision references.

## Implemented through Phase 9

### Operations and reconciliation

- Player and administrator statements with bounded output.
- Transaction search across IDs, external references, purposes, and durable details.
- Treasury-funded, reason-required positive staff adjustments with administrator attribution.
- Durable reconciliation summaries and UTF-8 CSV audit exports.
- Separate atomic operations storage for scheduled programs, reconciliations, and adjustment evidence.
- Configurable balance and transfer limits, daily caps, cooldowns, and exemptions.
- Conservative startup recovery and staff-reviewed correction entries.

### Hangar+ and TROA plugin integration

- Market purchase holds, seller captures, buyer refunds, listing fees, Blackmarket fees, and commodity escrow.
- Stable external transaction references so retries never charge twice.
- Capability discovery and semantic API versions.
- Per-plugin permissions, limits, audit labels, and circuit breakers.
- Optional webhook events that retain each plugin's configured Discord identity.

### Treasuries, taxes, and fees

- Server and faction treasuries.
- Sales tax, transfer tax, listing fees, station fees, docking/storage fees, and configurable sinks.
- Region, station, faction, item-category, and reputation-based policy rules.
- Tax exemptions and transparent transaction breakdowns.
- Scheduled treasury reports and sink/source analytics.

### Scheduled economy

- One-time or recurring faction-treasury payroll, rewards, bounties, contracts, and deposits.
- Funded-balance validation before every treasury debit.
- Durable per-run idempotency references and automatic pause into `RecoveryRequired` on an unresolved payout.
- Automatic polling with native banking dispatched onto the Space Engineers game thread.
- Pause, resume, cancel, list, and manual due-run administrator controls.

### Risk controls and reputation

- Configurable API-plugin allowlist enforced before escrow debit preparation.
- Rolling transaction-count and credit-volume limits plus a daily payer limit.
- Durable anomaly flags for denied plugins, velocity limits, daily limits, and unusually large accepted transfers.
- Non-punitive reputation signals based on completed, failed, and recovery-required outcomes.
- Risk controls default on; reputation is informational and never bans a player automatically.

### Nexus authority safeguards

- Explicit local server ID and designated authority server ID.
- Single-writer checks before cross-server economy work.
- Short durable player leases to prevent concurrent writers.
- Durable replay IDs and payload-hash conflict rejection.
- Nexus synchronization defaults off. Econ+ supplies authority safeguards, not a bundled Nexus transport adapter; an approved adapter must deliver authenticated messages into these guards.

### Analytics

- UTC-window transaction totals, completed volume, fees, taxes, refunds, held funds, failures, recovery counts, and unique-player counts.
- Server-local CSV audit exports remain separate from Discord notifications.
- Analytics read durable ledger state and never alter balances.

### Optional credit products

- Administrator-approved, treasury-funded loans with configurable principal, APR, term, payment interval, and per-player active-loan limits.
- Interest accrues explicitly by completed UTC days; no hidden compounding timer changes native balances.
- Borrower repayments use the same prepared/debit/held/credit/completed checkpoint model as other transfers.
- Ambiguous loan operations enter `RecoveryRequired`; there are no automatic collections or collateral seizures.
- Credit products default off and should remain disabled until the server has validated escrow and recovery behavior in production.

### Banking tools

- Player-to-player transfers and payment requests.
- Named accounts and server-approved organization wallets.
- Deposit holds, withdrawal limits, freeze/unfreeze, and staff recovery.
- Optional loans, interest, collateral references, and default handling after the safe core is proven.

### Cross-server economy

- Optional Nexus v3 server discovery and balance authority routing.
- Globally unique transactions, replay protection, custody locks, reconciliation, and recovery queues.
- Aggregated network analytics without broadcasting private player balances.

### Risk, reputation, and analytics

- Velocity limits and duplicate/replay detection.
- Configurable anomaly flags without automatic punitive action by default.
- Economy supply, sinks, sources, transaction volume, treasury movement, and plugin usage metrics.
- Reputation inputs exposed to Hangar+, Blackmarket, contracts, and other TROA systems.

## Commands

```text
!econ help
!econ balance
!econ pay <steam-id> <credits>
!econ history <count>
!econ statement <count>
!econ programs
!econ reputation
!econ loans
!econ loan repay <loan-id> <credits>

!econadmin help
!econadmin status
!econadmin reload
!econadmin search <text> <count>
!econadmin statement <steam-id> <count>
!econadmin adjust <steam-id> <credits> "reason"
!econadmin reconcile "note"
!econadmin export [steam-id]
!econadmin program add <Payroll|Reward|Bounty|Contract|Deposit> <steam-id> <credits> <interval-minutes> "name"
!econadmin program list
!econadmin program pause <id>
!econadmin program resume <id>
!econadmin program cancel <id>
!econadmin program run
!econadmin risk
!econadmin anomalies <count>
!econadmin reputation <steam-id>
!econadmin nexus
!econadmin nexus lease <steam-id>
!econadmin analytics <days>
!econadmin loan issue <steam-id> <credits> <days> "purpose"
!econadmin loan list [steam-id]
!econadmin recovery
!econadmin recovery inspect <transaction-id-or-unique-prefix>
!econadmin recovery finalize <transaction-id-or-prefix> <revision> <Completed|Refunded|Failed> "<audit note>"
!econadmin webhook
!econadmin webhook test
!econadmin escrowtest
!econadmin treasury
```

## Integration example

Hangar+ first discovers Econ+ and verifies the semantic version and required capabilities, then creates one stable transaction reference for a purchase:

```text
EconPlusApiRegistry.TryDiscover("Hangar+", "1.1.0", out api, out capabilities, out error)
TryCreateHold("Hangar+", purchaseId, buyerSteamId, amount, "Market purchase")
TryCaptureHold(transactionId, sellerSteamId)
```

If grid settlement fails before capture, Hangar+ calls `TryReleaseHold`. Repeating the same external reference returns the existing record rather than charging the buyer again.

Consumer plugins must call economy operations on the Space Engineers game thread until a later Econ+ API version explicitly provides thread dispatch.

`!econadmin escrowtest` runs an isolated fake-bank contract suite covering duplicate hold, capture and release retries, conflicting idempotency references, restart retry, and capture-versus-release claims. It creates temporary test-ledger data beneath the plugin storage folder, removes it afterward, and never reads or changes a live Space Engineers balance.

## Treasury policy

Player transfers can apply a fee through `TransferFeePercent` and, when `EnableTaxes` is true, a separate tax through `PlayerTransferTaxPercent`. Each component rounds upward to a whole credit. `MaximumCombinedChargeCredits` can cap their combined value; `0` means no cap. `ExemptSteamIds` accepts comma- or semicolon-separated Steam ID64 values. `ExemptSourcePlugins` reserves equivalent plugin exemptions for policy-aware integrations.

Set `ChargeDestination` to `Treasury` to credit the configured `TreasuryFactionTag`, or `Sink` to remove the fee and tax from circulation. A failed payee credit reverses the treasury movement or sink accounting before the payer refund is confirmed. `!econadmin treasury` reports the active policy without printing exemption values.

## Storage

Compatibility paths:

```text
TROA-Econ-Plus.cfg
TROA-Econ-PlusData/
  EconPlusLedger.xml
  EconPlusOperations.xml
  EconPlusAdvanced.xml
AuditExports/
  EconPlus-Audit-YYYYMMDD-HHMMSS.csv
```

The TROA prefix remains in filenames for product and upgrade consistency. The default player-facing name is `Econ+` and is configurable.

## Scheduled economy setup

`EnablePayroll` is the master switch for automatic scheduled processing. `ScheduledJobPollSeconds` controls how often due work is checked, and `MaximumScheduledJobsPerRun` bounds each pass. `RequireFundedTreasuryPayouts` defaults to `true`; the faction named by `TreasuryFactionTag` must have enough native credits before a payout starts. Set a program interval to `0` for a one-time payout or a positive minute value for a repeating program.

Program definitions and run state survive restarts in `EconPlusOperations.xml`. Each run uses the program ID and durable revision as its external reference. A successful repeating run advances its next UTC execution. Any unresolved payout places the program in `RecoveryRequired` instead of repeatedly attempting another debit.

`Contract` and `Deposit` programs in Phase 5 are treasury-funded scheduled disbursements. Consumer-owned milestone escrow continues to use `IEconPlusApi` holds so Econ+ never takes custody of grids, inventories, or contract completion authority.

## Operations safety

`!econadmin adjust` only supports positive, treasury-funded corrections and requires a meaningful audit reason. It does not mint credits or silently edit ledger history. Negative corrections require staff to use native economy administration and then record/reconcile the outcome rather than allowing Econ+ to guess at custody.

`!econadmin reconcile` records totals and the number of ambiguous transactions at that point in time; it does not force a state change. `!econadmin export` writes a server-local CSV and never posts private player data to Discord.

## Phase 6–9 configuration

`EnableRiskControls`, `AllowedApiPlugins`, rolling velocity fields, and `DailyTransferLimitCredits` control phase 6. Add every trusted server-side API consumer to `AllowedApiPlugins`; player `!econ` transfers identify as Econ+ and remain governed by player limits.

For phase 7, leave `EnableNexusSynchronization` false on single-server installations. A Nexus deployment must assign a unique `NexusServerId`, configure exactly one `NexusAuthorityServerId`, and use an external authenticated transport adapter. The authority service rejects writes on non-authority servers and stores lease/replay evidence in `EconPlusAdvanced.xml`.

Phase 8 analytics are available through `!econadmin analytics <days>` and audit exports. Phase 9 requires `EnableCreditProducts=true`; all loans debit the configured treasury before borrower credit and therefore cannot issue unfunded principal when funded payouts are required.

If `EconPlusLedger.xml` cannot be read, Econ+ preserves the original as a timestamped `.corrupt-*` file and stops economy startup. It never replaces an unreadable authoritative ledger with an empty one.

## Recovery finalization

`recovery finalize` does not transfer, refund, create, or destroy credits. Before using it, staff must independently verify the payer, payee, and treasury balances in the native Space Engineers economy and reconcile the related consumer plugin. Run `recovery inspect`, use the exact displayed revision, choose the verified final state, and provide a quoted audit note of 10–500 characters. Econ+ rejects stale revisions, non-recovery transactions, duplicate finalization, and missing notes. Each accepted decision is appended to the ledger with its administrator Steam ID (or `0` for console), previous state/detail, final state, timestamp, and note.

## Discord audit webhook

Discord delivery is optional, asynchronous, bounded, and notification-only. Enable it with `EnableDiscordWebhook`, set a full HTTPS Discord webhook URL, and choose the external webhook name with `DiscordWebhookName`. Independent switches control Completed, Refunded, Failed, and RecoveryRequired events; Failed notifications default off while recovery alerts default on. Each actual state transition queues at most one notice. Compatible API retries that return an existing terminal transaction do not queue another notice.

Webhook embeds contain only the short transaction ID, source plugin label, result state, amount, and fee. They do not contain Steam IDs, external references, recovery notes, webhook URLs, or checkpoint details. Delivery failures and a full queue are logged without exposing the URL and never alter an authoritative transaction.

## Safety status

This is an alpha foundation. Back up the server before testing. A restart marks an unconfirmed banking attempt as `RecoveryRequired`; a prepared record with no debit attempt is safely failed, and a durably confirmed hold stays held for its consumer. Econ+ never guesses an ambiguous transaction into a final state, and its recovery-finalization command only records a result that staff already verified outside the plugin.

The public repository distributes approved binaries, configuration examples, and documentation only. Use and redistribution are governed by `LICENSE.md`; support and safe issue-reporting guidance are in `SUPPORT.md`.
