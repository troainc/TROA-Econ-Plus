# TROA Econ+ Changelog

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
