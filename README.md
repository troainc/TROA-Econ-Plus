# TROA Econ+

TROA Econ+ is a server-side Torch economy plugin for Space Engineers. It provides durable accounting, player payments, escrow, treasury policy, recovery tools, and a versioned API for Hangar+ and other TROA plugins.

> Status: `v0.3.0-alpha`  
> Runtime: Torch / .NET Framework 4.8 / x64  
> UI requirements: none

Players use Space Engineers chat commands. Owners use chat commands and XML configuration. Econ+ requires no client mod, desktop app, web panel, WPF, WinForms, or custom terminal UI.

## Current alpha features

- Native Space Engineers credits remain authoritative.
- Durable transaction ledger with prepared, held, completed, refunded, failed, and recovery states.
- Durable checkpoints before native debits, credits, treasury movements, reversals, and refunds.
- Strict duplicate-reference matching that rejects a reused reference with different transaction details.
- Conservative restart classification and corrupt-ledger preservation.
- Player balance, direct payment, and recent transaction-history commands.
- Configurable transfer minimum, maximum, cooldown, fee, and treasury faction.
- Durable escrow holds that integrated plugins can capture or refund.
- Retry-safe external references that prevent duplicate plugin charges.
- Semantic API v1.1 discovery and capability reporting for Hangar+ and other TROA plugins.
- Mutually exclusive capture/release claims prevent the same hold from being both credited and refunded.
- Isolated escrow contract checks cover retry, conflict, restart, and settlement-claim behavior without live balances.
- Separate transfer fees and taxes with deterministic rounding, optional charge caps, Steam-ID exemptions, and treasury-or-sink routing.
- Administrator status, configuration reload, and recovery review.
- Atomic ledger saves with backups.
- Windows and Linux/AMP/Wine-compatible paths.

## Commands

```text
!econ help
!econ balance
!econ pay <steam-id> <credits>
!econ history <count>

!econadmin status
!econadmin reload
!econadmin recovery
!econadmin recovery inspect <transaction-id-or-unique-prefix>
!econadmin recovery finalize <transaction-id-or-prefix> <revision> <Completed|Refunded|Failed> "<audit note>"
!econadmin webhook
!econadmin webhook test
!econadmin escrowtest
!econadmin treasury
```

Recovery finalization never moves credits. Staff must independently verify native payer, payee, and treasury balances, inspect the current transaction revision, and then record the verified result with a required quoted audit note. Stale revisions and duplicate finalization are rejected.

## Installation

1. Back up the world and Torch plugin data.
2. Install the approved `TROA-Econ-Plus-v0.3.0-alpha.zip` through Torch.
3. Restart Torch.
4. Econ+ creates `TROA-Econ-Plus.cfg` and `TROA-Econ-PlusData`.
5. Run `!econadmin status` as a Torch administrator.
6. Test a small payment on a development server before production use.

## Configuration

See [TROA-Econ-Plus.cfg.example](TROA-Econ-Plus.cfg.example). `MaximumEscrowCredits` limits a single plugin hold. `TransferFeePercent` and `PlayerTransferTaxPercent` control player-transfer charges; `MaximumCombinedChargeCredits` can cap them. `ExemptSteamIds` accepts comma- or semicolon-separated Steam ID64 values. `ChargeDestination` selects the configured faction `Treasury` or an economy `Sink`. Payroll, taxes, and Nexus synchronization remain reserved settings; Discord audit delivery is optional.

## Hangar+ integration

Econ+ owns credit holds, captures, refunds, fees, and financial recovery. Hangar+ owns grids, market listings, commodity custody, and claims. Nexus owns routing. Discord is notification-only.

```text
Hangar+ validates listing and grid custody
        |
        v
Econ+ creates retry-safe buyer hold
        |
        v
Hangar+ transfers grid custody
        |
        v
Econ+ captures seller payment
        |
        v
Both plugins record completion
```

If grid settlement fails before capture, Hangar+ releases the hold. Repeating the same external reference returns the existing transaction rather than charging twice. API v1.1 now provides `EconPlusApiRegistry.TryDiscover`, semantic minimum-version checks, capability flags, configurable escrow limits, and retry-safe hold/capture/release behavior. Consumer calls remain game-thread-only.

## Discord audit webhook

Discord audit delivery is optional, asynchronous, and notification-only. Owners control the external name with `DiscordWebhookName` and can independently enable Completed, Refunded, Failed, and RecoveryRequired notices. Failed notices default off; recovery alerts default on. Notices contain no Steam IDs, external references, recovery notes, webhook URLs, or checkpoint details. Delivery failures never change a transaction.

## Planned features

- Double-entry views, statements, search, reconciliation, and correction entries.
- Player, faction, alliance, station, server, and system accounts.
- Taxes, fees, exemptions, economy sinks, and server/faction treasuries.
- Funded payroll, stipends, activity rewards, bounties, contracts, deposits, and event prizes.
- Plugin permissions, daily limits, account freezes, velocity controls, and anomaly flags.
- Nexus v3 cross-server authority, replay protection, locks, and reconciliation.
- Supply, sink, source, volume, treasury, and plugin-usage analytics.
- Reputation signals for Hangar+, Blackmarket, contracts, and other TROA plugins.
- Optional organization wallets, payment requests, loans, interest, and collateral only after the safe core is proven.

## Storage and recovery

```text
TROA-Econ-Plus.cfg
TROA-Econ-PlusData/
  EconPlusLedger.xml
  EconPlusLedger.xml.bak
  EconPlusLedger.xml.corrupt-<timestamp>  (only when an unreadable ledger is preserved)
```

Steam ID64 is the stable player reference. Native identity IDs are resolved only when banking operations occur. Back up both paths before wipes, upgrades, storage moves, or policy changes. Never delete a held or recovery-required transaction without reconciling native balances and the consumer plugin. Recovery decisions remain appended to the ledger with the administrator, previous state, verified result, time, and note. Econ+ stops economy startup rather than replacing an unreadable ledger with an empty one.

## Alpha warning

This is an early development-server foundation. Keep backups and start with small limits. Do not describe roadmap features as active until released.

See [CHANGELOG.md](CHANGELOG.md), [ROADMAP.md](ROADMAP.md), [LICENSE.md](LICENSE.md), and [SUPPORT.md](SUPPORT.md).
