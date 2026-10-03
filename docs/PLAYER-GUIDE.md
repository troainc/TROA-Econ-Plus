# Player Guide

Econ+ runs through Space Engineers chat. Type `!econ help` in game to see the commands available on your server. A server owner may disable any feature below, so a disabled command can be expected.

## Your account and payments

```text
!econ dashboard
!econ balance
!econ pay <steam-id> <credits>
!econ payto <name-or-steam-id> <credits>
!econ history <count>
!econ statement <count>
```

Your Econ+ account is keyed to your Steam ID. `!econ dashboard` shows your Econ+ account; `!econ balance` shows the native Space Engineers/Keen balance, which may be a compatibility mirror. The server chooses starting credits, currency name, transfer limits, and any fees. Confirm the recipient Steam ID before using `!econ pay`. `!econ payto` can use a known player name or Steam ID. If you need help resolving a payment, give an administrator the approximate time and amount; do not post private account data in a public channel.

## Browse and trade physical items

```text
!econ trade
!econ trade <page>
!econ trade search <name or symbol>
!econ trade quote <symbol>
!econ trade buy <symbol> <quantity>
!econ trade sell <symbol> <quantity>
!econ trade holdings
```

The item market is not a ship market. It trades physical item types such as ore, ingots, components, ammo, tools, and supported modded items. Find the exact symbol with `!econ trade search` or ask staff for the server's `MarketCatalog.csv` entry.

If the server requires a station, travel within the configured range of a grid whose name contains the station tag (usually `[ECON+ STATION]`). Buy orders may need free inventory space. With physical delivery enabled, selling removes the real items and buying gives real items; virtual-only mode behaves differently.

## Player shops

```text
!econ shop
!econ shop <page>
!econ shop sell <symbol> <quantity> <price>
!econ shop buy <listing-id> <quantity>
!econ shop mine
!econ shop cancel <listing-id>
```

Player listings are separate from the server's NPC commodity market. Check the listing and price before buying. Cancelling a listing returns unsold goods according to the server's delivery settings.

## Accounts and scheduled payments

If enabled, named savings/business accounts and repeating payments are managed with:

```text
!econ accounts
!econ account create <Savings|Business> "<name>"
!econ account deposit <account-id> <credits>
!econ account withdraw <account-id> <credits>
!econ account pay <account-id> <player> <credits> "purpose"
!econ schedule add <Payment|Rent|Subscription|Tax> <player> <credits> <interval-minutes> "purpose"
!econ schedules
!econ schedule pause <id>
!econ schedule resume <id>
!econ schedule cancel <id>
```

Use `!econ accounts` to find account IDs before depositing or withdrawing. The server can limit account types, scheduled payments, intervals, and who may pay from managed accounts.

## Loans and credit score

```text
!econ creditscore
!econ loans
!econ loanapply <credits> <days> "purpose"
!econ loan repay <loan-id> <credits>
!econ loan refinance <loan-id> <days>
```

Loan availability, eligibility, amount, interest, due dates, and refinancing are controlled by server policy and treasury funding. An application is not a guarantee of approval.

## Faction payroll and managed treasuries

Faction treasuries are managed accounts. If you have permission, use `!econ accounts` to find the treasury and its account ID, then use the named-account deposit, withdraw, or pay commands. The server owner controls who can manage it and any daily spending or approval limits. Faction payroll is a separate command available to an authorized faction founder or leader with access to a funded treasury.

```text
!econ payroll preview <credits-per-member>
!econ payroll <credits-per-member> ["purpose"]
```

Preview payroll before running it. A faction needs a funded Econ+ treasury, and the founder/leader role, spending limits, cooldown, and recipient limits apply. `!econ programs` lists administrator-created programs; it does not control faction payroll.

## Optional server features

When enabled, the server may also offer:

- `!econ tax` and `!econ tax pay [amount]` for government tax status and payment.
- `!econ route <symbol>` for commodity prices across territories.
- `!econ insure <grid name> <value>`, `!econ policies`, and `!econ policy cancel <id>` for ship insurance.
- `!econ contracts`, `!econ contract post <Delivery|Bounty|Escort|Custom> <reward> "description"`, `!econ contract accept <id>`, `!econ contract complete <id>`, and `!econ contract cancel <id>` for jobs.
- `!econ invest`, `!econ invest quote <symbol>`, `!econ invest buy <symbol> <quantity>`, `!econ invest sell <symbol> <quantity>`, and `!econ invest portfolio` for the investment exchange.
- `!econ alert <symbol> gt|lt <price>`, `!econ alerts`, and `!econ alert cancel <id>` for price alerts.
- `!econ order buy|sell <symbol> <quantity> <price>`, `!econ orders`, and `!econ order cancel <id>` for limit orders.
- `!econ top [count]` and `!econ movers` for leaderboards and market movement.
- `!econ gov`, `!econ elections`, `!econ vote <election-id> <faction-tag>`, `!econ bonds`, `!econ bond buy <series-id> <units>`, and `!econ bonds mine` for enabled government systems.

The available command list shown by `!econ help` is the final authority for your server and installed plugin version.

## LCD panels

An owner can place an LCD/text surface with `[ECON+]` in the block name. Set its Custom Data, for example:

```text
Template=Dashboard
Render=Sprite
```

Account panels show information for the panel owner. Common templates include `Dashboard`, `Bank`, `Loan`, `Tax`, and `Help`. Shared boards include `Station`, `Exchange`, `Government`, `Contracts`, `Route`, `Insurance`, and `Bonds`. Some boards need additional keys such as `Symbol=`, `Items=`, `Types=`, `Title=`, or `FactionTag=`. Use `Render=Text` if sprite graphics do not display on that surface. Ask your server owner which boards and templates the installed version supports.
