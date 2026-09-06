# TROA Econ+ Changelog

## v1.2.2-alpha - Catalog Browsing, Panel Curation, Exports, and LCD Polish

- `!econ trade` is now paginated ("Page P/T"); `!econ trade <page>` flips pages and
  `!econ trade search <text>` filters by symbol or name, so the whole catalog is reachable.
- Station and Exchange LCD panels are curated per panel from Custom Data: `Items=IRON,GOLD`
  (explicit symbols) and/or `Types=Ore,Ingot` (categories), plus `Title=` to rename a panel. A
  panel with no filter shows the full board, so different stations can show different goods.
- Every scan exports owner reference files to the Econ+ data folder - `MarketCatalog.csv` (every
  commodity symbol, item, and price) and `ExchangeCatalog.csv` - so nothing has to be remembered;
  `!econadmin marketscan` reports the export.
- LCD panels render in a cleaner style: monospace columns, right-aligned prices, per-board colour
  schemes (market green, exchange amber, bank cyan), title bars, dividers, an "updated" line, and
  up/down change arrows on the exchange.
- All listed items use dynamic pricing (scanned items carry elasticity and demand scale); no item
  is statically priced.
- Verified: Release build zero warnings/errors; boundary 2/2; escrow 21/21; market 15/15;
  invest 13/13.

## v1.2.1-alpha - Real Item Catalog and Physical Delivery

- The commodity market now populates from the server's actual physical item definitions - Keen
  vanilla and modded - instead of a small hardcoded list. On world load Econ+ scans
  `MyDefinitionManager`, prices each item from its `MinimalPricePerUnit` (with a configurable
  fallback), stores the exact item type/subtype for delivery, and lists it for trading.
- Prints a startup summary to the Torch console, e.g. "scanned N physical items found (V vanilla,
  M modded); added A, market now lists T commodities." `!econadmin marketscan` re-runs the scan
  and reports counts in chat.
- Existing commodities are preserved across restarts (deduped by item type and subtype), so prices
  and supply/demand state are never reset by a rescan.
- Physical delivery is now on by default (`EnablePhysicalDelivery=true`): buying deposits the real
  item into the player's inventory and selling removes it. Delivery failures are logged to the
  Torch console.
- Adds catalog configuration (item-type filter, price fallback and bounds, elasticity/scale) and
  caps the `!econ trade` list and `Station` LCD so large modded catalogs stay readable; trading by
  symbol works for every item.
- Verified: Release build zero warnings/errors; boundary 2/2; escrow 21/21; market 15/15;
  invest 13/13. Physical delivery remains compile- and API-verified pending live-server testing.

## v1.2.0-alpha - Full Economic Ecosystem

The economy was rebuilt so that Econ+ owns all money state and the native Space Engineers
("Keen") economy is used only to add/remove credits and show in-game messages. Accounts, banks,
treasuries, a dynamic commodity market, and an investment exchange are now owned by Econ+ and
surfaced through chat commands, LCD panels, and the server-side plugin API.

### Keen boundary
- Econ+ is the sole authoritative owner of every credit balance; balances live in the internal
  account store and all money movement routes through one balance service.
- Native `MyBankingSystem` access is confined to that balance service (the optional mirror plus
  first-use import and reconciliation). Transfers, payroll, loans, escrow, named accounts, and
  faction treasuries no longer touch Keen banking, Keen faction accounts, or Keen account creation.
- `EconomyBoundarySelfTest` (`!econadmin boundarytest`) scans the compiled plugin and fails if any
  code path outside the mirror references native Keen banking.

### In-game messaging
- A single server-to-player message channel (`EconomyMessageService`) delivers asynchronous
  notices - received/sent payments, treasury payouts, scheduled payments, and loan reminders -
  through the Torch chat manager, with per-event configuration toggles.

### LCD ecosystem displays
- New panel templates alongside the existing ones: `Bank` (balance, credit score, and managed
  accounts), `Station` (live commodity board), and `Exchange` (live share/index ticker). Station
  and Exchange render on any tagged panel regardless of owner.

### Commodity market
- A dynamic commodity market: prices float on net supply and demand, bounded per commodity and
  mean-reverting to base over time.
- Credit-settled through the authoritative accounts with the treasury as market maker, so credits
  are conserved (buys pay the treasury; sells are funded by it).
- Player commands `!econ trade [quote|buy|sell|holdings]`, with buy/sell gated to trade-station
  proximity (a grid named with the configurable station tag).
- Optional physical delivery (`EnablePhysicalDelivery`, default off) moves real Space Engineers
  items in and out of the player's inventory, using the inventory system only and kept separate
  from the credit boundary, with dupe-safe ordering.
- Plugin-API surface `IEconPlusMarketApi` and the `CommodityMarket` capability.

### Investment exchange
- Abstract shares and indices whose prices move on player flow and on a simulated drift that
  random-walks each tick, bounded per instrument and credit-settled like the commodity market.
- Player commands `!econ invest [quote|buy|sell|portfolio]` with per-player positions.
- Plugin-API surface `IEconPlusInvestApi` and the `InvestmentExchange` capability.

### Validation
- .NET Framework 4.8 x64 Release build with zero warnings and zero errors.
- Isolated self-tests all pass: Keen boundary, escrow contract, commodity market, and investment
  exchange. Physical goods delivery is compile- and API-verified and awaits live-server testing.
