# Server Owner Setup Guide

This guide takes a fresh Torch server from plugin installation to a basic, tested player economy. Start with the standard account and transfer features. Add markets, taxes, payroll, credit products, and government systems only when you are ready to administer them.

## 1. Before installation

1. Confirm your Torch server is running the Space Engineers version supported by the Econ+ package. Econ+ is a Torch plugin for .NET Framework 4.8 x64; players do not install a client mod.
2. Stop the server and make a restorable copy of the world, the Torch instance, the current Econ+ configuration, and the full `TROA-Econ-PlusData` directory.
3. Keep the exact version of the plugin package you install. Read the release notes before upgrading, especially when moving an existing economy.

## 2. Install and start once

1. Install the release package using Torch's normal plugin installation method. Use the package for the exact release; do not install this documentation repository as a plugin.
2. Start the server and confirm that Torch reports Econ+ initialized without a configuration or storage error.
3. Econ+ creates `TROA-Econ-Plus.cfg` in its Torch plugin storage folder. Stop the server before editing it directly. The location can vary by Torch instance, so use that instance's plugin storage path rather than copying another server's path.
4. The public [`TROA-Econ-Plus.cfg.example`](../TROA-Econ-Plus.cfg.example) is a complete reference. It is an example, not a replacement for the configuration file already generated for your server.

The plugin data folder is `TROA-Econ-PlusData` beside the plugin's configured storage location. It holds Econ+ account and transaction data, market state, and other enabled feature records. Preserve the whole folder during backup and migration.

## 3. Choose your account behavior

Review these settings before players use the economy:

| Setting | What it means | Example/default behavior |
|---|---|---|
| `UseInternalAccounts` | Econ+ stores its authoritative balances by SteamID64. | `true` |
| `NewAccountStartingCredits` | Starting credits for a newly created Econ+ account. | `10000` |
| `ImportKeenBalanceOnFirstUse` | Imports a player's current Keen balance once when their Econ+ account is first created. | `true` in the sample |
| `MirrorBalancesToKeen` | Mirrors balance changes to Keen when possible for compatibility. Econ+ remains authoritative. | `true` in the sample |
| `CurrencyName`, `CurrencySymbol` | Player-facing currency label and short symbol. | `credits`, `cr` |
| `EnableOfflinePayments` | Allows payments to a known offline Steam ID. | `true` in the sample |

Decide whether first-use imports fit your economy before players create accounts. If you already have a live economy, do not casually reset, replace, or hand-edit account or ledger files. Review the migration and recovery instructions first.

## 4. Set player transfer policy

The sample enables `EnablePlayerTransfers` and starts with zero fees and taxes. Tune `MinimumTransferCredits`, `MaximumTransferCredits`, and `TransferCooldownSeconds` to your server. If you later set a transfer fee or player transfer tax, check `ChargeDestination`, `TreasuryFactionTag`, and any exemptions. Fees and taxes are separate percentages; `MaximumCombinedChargeCredits` can cap their combined charge (`0` means no cap).

Fees routed to a treasury require a matching Econ+ treasury account. If you are not ready to manage a treasury, keep transfer percentages at `0`.

To create a faction treasury, use `!econadmin factionaccount create <faction-tag> "<account-name>"`. Review the created account ID and grant manager access only to trusted faction staff. Authorized members can deposit into the managed account using `!econ account deposit <account-id> <credits>`; treasury withdrawals and payments follow the configured daily limit and approval threshold. Create and fund the treasury before routing fees, taxes, payroll, or loans to it.

## Existing economy migration

Do not copy old balance files over the active Econ+ data folder. For a supported import, enable `EnableMigrationImports`, place the prepared CSV/XML file under `TROA-Econ-PlusData/Imports`, and review the command's accepted format and mode before importing:

```text
!econadmin accounts import <filename> <merge|replace> IMPORT
```

Take a fresh world/config/data backup first. Run the import on a test copy of the server before production, and inspect the resulting accounts and signed backup. Treat `replace` as a migration operation requiring explicit review; never use it as a routine repair shortcut.

## 5. Enable and test the item market (optional)

The sample enables the commodity market and physical delivery. At startup Econ+ scans the server's registered physical item definitions, including modded items. Use:

```text
!econadmin marketscan
!econ trade
!econ trade search iron
!econ trade quote IRON
```

To require a nearby station, leave `RequireStationProximity=true` and name a station grid with the configured `StationNameTag` (default `[ECON+ STATION]`). Players must be within `StationRadiusMeters` (default 150 m). Use `!econ trade buy IRON 10` or `!econ trade sell IRON 10` while in range. The exact symbol is in `TROA-Econ-PlusData/MarketCatalog.csv`; that catalog is generated for the server's vanilla and modded definitions.

`EnablePhysicalDelivery=true` means Econ+ removes or grants real items during a trade. Test with small quantities and verify inventory capacity. Set `MarketItemTypes` only if you want to restrict the entire market by item category; leave it empty to include every scanned category. See the [Configuration Guide](CONFIGURATION.md#commodity-market-and-stations).

## 6. Reload, verify, and announce

After changing configuration, use `!econadmin reload` if available in the installed build, then check `!econadmin status` and Torch logs. If you changed a setting that is only read at world/plugin startup, stop and restart Torch. Confirm the plugin's startup log and run these checks from an admin account:

```text
!econadmin status
!econadmin boundarytest
!econadmin escrowtest
!econ balance
!econ help
```

Use the isolated `boundarytest` and `escrowtest` commands included by your installed build; neither is a production transaction. Test actual transfers and any enabled market trade on a development server or with small amounts before announcing the feature. Use `!econadmin webhook test` only after configuring the Discord webhook.

## 7. Back up and protect the economy

- Back up the entire `TROA-Econ-PlusData` folder plus the world and config using a consistent stopped-server snapshot.
- The sample enables signed backups. `!econadmin backup [label]` creates a plugin backup; still keep an independent server backup.
- Do not edit ledger/account files while the server is running. Do not delete a corrupt or ambiguous ledger and let a new empty one replace it.
- If an economy transaction is incomplete after a crash, use `!econadmin recovery` and `!econadmin recovery inspect <id>` to inspect persisted state. Staff must verify native balances and related consumer-plugin state before finalizing recovery. `recovery finalize` records a verified decision; it does not move credits.
- During an incident, `!econadmin maintenance on "reason"` freezes balance-changing player and plugin operations; use `!econadmin maintenance off` after the issue is resolved.

See [Troubleshooting and Data Safety](TROUBLESHOOTING.md) before restoring data or resolving a transaction.

## Optional features

| Feature | Before turning it on |
|---|---|
| Faction accounts/payroll | Create and fund a faction treasury; choose approval thresholds, spending caps, founder/leader policy, and payroll limits. See `EnableFactionAccounts` and `EnableFactionPayroll`. |
| Government tax | Choose a government faction and create its Econ+ treasury first. Review assessment interval, arrears grace, late fee, collection cap, exemptions, and suspension behavior. Tax is off by default. |
| Territories/government | Create a funded faction treasury, charter a government or use the supported preset, and add carefully named grid zones. Taxes, fees, tariffs, royalties, stipends, elections, and bonds are separate settings. These are off by default. |
| Insurance | Choose and fund the insurer treasury, set premium and payout bounds, then coordinate claims with Hangar+ or an authorized admin workflow. Disabled by default. |
| Contracts | Decide who may post jobs and escrow credits. Test post, accept, complete, cancel, and expiry behavior. Disabled by default. |
| Loans and credit products | Review loan limits, interest, term, repayment, late fee, eligibility score, and treasury funding. Do not enable loans until you understand the funding source. |
| Discord audit webhook | Create a private Discord webhook, keep its URL private, and configure notification switches. Discord is notification-only and never controls transaction outcomes. |
| Nexus | Leave synchronization disabled on a single-server setup. Multi-server synchronization requires a unique server ID, one authority, and an authenticated transport integration. |

Use the [Configuration Guide](CONFIGURATION.md) for feature switches, dependencies, and setup order.
