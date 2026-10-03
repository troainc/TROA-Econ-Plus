# Command Reference

Type commands in Space Engineers chat. `!econ help` and `!econadmin help` list commands registered by the installed build. Features may be disabled, and newer commands may not exist in older plugin versions.

## Player commands

| Command | Purpose |
|---|---|
| `!econ help` | List player commands. |
| `!econ dashboard` | Check your Econ+ account overview. |
| `!econ balance` | Check your native Space Engineers/Keen balance mirror. |
| `!econ pay <steam-id> <credits>` | Send a player payment. |
| `!econ payto <name-or-steam-id> <credits>` | Pay a known online or offline player by name or Steam ID. |
| `!econ history <count>`, `!econ statement <count>` | Review personal account activity. |
| `!econ programs`, `!econ reputation`, `!econ risk` | View scheduled programs, reputation, and personal limits. |
| `!econ accounts` | List available checking, savings, business, and managed faction accounts. |
| `!econ account create <Savings|Business> "<name>"` | Create a named account, when allowed. |
| `!econ account deposit|withdraw <account-id> <credits>` | Move credits between checking and a named account. |
| `!econ account pay <account-id> <player> <credits> "purpose"` | Pay from a permitted named account. |
| `!econ schedule add <type> <player> <credits> <interval-minutes> "purpose"` | Create a scheduled Payment, Rent, Subscription, or Tax entry. |
| `!econ schedules`, `!econ schedule pause|resume|cancel <id>` | Manage scheduled payments. |
| `!econ trade [page]`, `!econ trade search <text>` | Browse or search the commodity market. |
| `!econ trade quote <symbol>`, `!econ trade holdings` | Inspect price or your positions. |
| `!econ trade buy|sell <symbol> <quantity>` | Buy or sell commodities. |
| `!econ shop [page]` | Browse player listings. |
| `!econ shop sell <symbol> <quantity> <price>` | Create a listing. |
| `!econ shop buy <id> <quantity>`, `!econ shop mine`, `!econ shop cancel <id>` | Buy, view, or cancel listings. |
| `!econ invest`, `!econ invest quote <symbol>`, `!econ invest portfolio` | Browse or inspect investments. |
| `!econ invest buy|sell <symbol> <quantity>` | Trade investment instruments. |
| `!econ loans`, `!econ creditscore` | View loans and score. |
| `!econ loanapply <credits> <days> "purpose"` | Apply for a loan if enabled. |
| `!econ loan repay <loan-id> <credits>`, `!econ loan refinance <loan-id> <days>` | Repay or request refinancing. |
| `!econ payroll preview <credits-per-member>` | Preview eligible faction payroll recipients and total cost. |
| `!econ payroll <credits-per-member> ["purpose"]` | Run an authorized faction payroll. |
| `!econ tax`, `!econ tax pay [amount]` | View or pay enabled government tax. |
| `!econ contracts`, `!econ contract post|accept|complete|cancel ...` | Browse and use the enabled contract board. |
| `!econ insure <grid> <value>`, `!econ policies`, `!econ policy cancel <id>` | Manage enabled insurance policies. |
| `!econ route <symbol>` | Compare a commodity's territory prices. |
| `!econ alert <symbol> gt|lt <price>`, `!econ alerts`, `!econ alert cancel <id>` | Manage price alerts. |
| `!econ order buy|sell <symbol> <quantity> <price>`, `!econ orders`, `!econ order cancel <id>` | Manage limit orders. |
| `!econ gov`, `!econ elections`, `!econ vote <election-id> <faction-tag>` | View or participate in enabled government systems. |
| `!econ bonds`, `!econ bond buy <series-id> <units>`, `!econ bonds mine` | Browse and manage government bonds. |
| `!econ top [count]`, `!econ movers` | View wealth and market leaderboards. |

For player-focused instructions, see the [Player Guide](PLAYER-GUIDE.md).

## Administrator commands

These are examples of the owner/staff command areas. Exact permissions and available commands depend on plugin version and Torch rank. Run `!econadmin help` in game for the installed command list.

### Setup and checks

```text
!econadmin help
!econadmin status
!econadmin reload
!econadmin boundarytest
!econadmin escrowtest
!econadmin marketscan
!econadmin markettest
!econadmin investtest
!econadmin webhook
!econadmin webhook test
!econadmin treasury
```

### Accounts, ledger, and data

```text
!econadmin search <text> <count>
!econadmin statement <steam-id> <count>
!econadmin adjust <steam-id> <credits> "reason"
!econadmin reconcile "note"
!econadmin export [steam-id]
!econadmin backup [label]
!econadmin accounts export
!econadmin accounts rebuild preview
!econadmin accounts rebuild REBUILD
!econadmin accounts import <filename> <merge|replace> IMPORT
!econadmin keen import <steam-id> CONFIRM
!econadmin keen reconcile [steam-id]
!econadmin keen repair <steam-id> <expected-drift> CONFIRM
!econadmin recovery
!econadmin recovery inspect <transaction-id>
!econadmin recovery finalize <transaction-id> <revision> <Completed|Refunded|Failed> "verified reason"
```

Only finalize a recovery after checking native balances and the related consumer plugin. The command records the verified outcome; it does not move credits.

`!econadmin adjust` is a positive, audited, treasury-funded correction. It is not a balance editor or credit-minting command.

### Scheduled economy and banking

```text
!econadmin program add <Payroll|Reward|Bounty|Contract|Deposit> <steam-id> <credits> <interval-minutes> "name"
!econadmin program list
!econadmin program pause <id>
!econadmin program resume <id>
!econadmin program cancel <id>
!econadmin program run
!econadmin payrolls [count]
!econadmin factionaccount create <tag> "name"
!econadmin factionaccount grant <account-id> <steam-id>
!econadmin factionaccount revoke <account-id> <steam-id>
!econadmin factionaccount policy <account-id> <daily-limit> <approval-threshold>
!econadmin factionaccount approvals <account-id>
!econadmin factionaccount approve <request-id> CONFIRM
!econadmin loan issue <steam-id> <credits> <days> "purpose"
!econadmin loan list [steam-id]
```

### Tax, governments, and other modules

```text
!econadmin tax
!econadmin tax run
!econadmin tax status <steam-id>
!econadmin tax forgive <steam-id> CONFIRM
!econadmin gov list
!econadmin gov preset <tag>
!econadmin gov charter <tag> <Federal|Regional|Local> [parent-tag]
!econadmin gov remove <tag>
!econadmin gov policy <tag> <docking-fee> <territory-tax> <tariff%> <royalty%> <stipend>
!econadmin gov zone add <tag> <grid-name-tag> <radius> [planetary]
!econadmin gov zone remove <zone-id>
!econadmin gov pricemod <tag> <percent>
!econadmin gov contraband add <tag> <symbol>
!econadmin gov contraband remove <tag> <symbol>
!econadmin gov election open <scope> [minutes]
!econadmin gov election close <election-id>
!econadmin gov bond issue <tag> <face-value> <coupon%> <units> <term-minutes>
!econadmin insurance claims [count]
!econadmin insurance claim <steam-id> <policy-id-or-grid> <loss-value> [loss-ref]
!econadmin contract complete <contract-id> <steam-id>
!econadmin risk
!econadmin anomalies <count>
!econadmin reputation <steam-id>
!econadmin analytics <days>
!econadmin nexus
!econadmin nexus lease <steam-id>
!econadmin maintenance
!econadmin maintenance on "reason"
!econadmin maintenance off
```

Dangerous or financial commands have additional validation, permission, confirmation, and audit behavior. Review the config and feature guide before using them.
