# TROA Econ+ Changelog

## v1.6.0-alpha - Governing Body and Territorial Economy

- Adds a **federated governing body**. The server owner's faction is the overarching **United
  Faction** (a `Federal` government); any faction can be chartered as its own `Regional`/`Local`
  government over territory. New `EconomyGovernanceStore` (`EconPlusGovernance.xml`) and
  `EconomyGovernanceService`. Off by default; every credit movement stays in the authoritative
  accounting layer and jurisdiction is a world-state read only (no native Keen banking).
- **Territories (space and planets):** a government owns **zones** around named grids (radius +
  optional `planetary` flag), reusing the trade-station proximity primitive. A player's live
  position resolves to the nearest owning zone, else the federal government.
- **Territorial revenue** routed to the jurisdiction's treasury with an optional `FederalCutPercent`
  to the United treasury: **docking fees** (once per cooldown), **trade tariffs** on market/shop
  buys, **extraction royalties** on sells, and periodic **territory tax** on active residents.
- **Spending:** **citizen stipend / UBI** pays active residents from a government treasury each
  cycle; **government bonds** (`!econadmin gov bond issue`, `!econ bond buy`) pay coupons and
  principal automatically from the issuing treasury.
- **Governance & politics:** player self-charter (`!econ charter`, optional fee to the United
  treasury), **elections** (`!econadmin gov election open`/`close`, `!econ vote`), a `Government`
  LCD template (public budget board), and `!econ gov` for the current jurisdiction.
- **Central bank:** a federal loan-APR override (`!econadmin gov apr`, gated by
  `EnableCentralBankAprOverride`) sets loan interest economy-wide via `EconomyCreditService`.
- **Enforcement:** the unpaid-tax suspension now also blocks **market and shop buys** (in addition
  to transfers and loan applications).
- **Turnkey adoption:** `!econadmin gov preset <tag>` charters a faction as the federal United
  government and enables territories/fees/tariffs/royalties in one step. (Cross-server Nexus
  federation remains future work; Nexus transport is still disabled/stubbed.)
- New config block (all defaults off/zero, backward compatible): `EnableTerritories`,
  `EnableDockingFees`, `DockingFeeCooldownSeconds`, `EnableTerritoryTariffs`,
  `EnableExtractionRoyalty`, `FederalCutPercent`, `MaxDockingFeeCredits`, `MaxTerritoryTariffPercent`,
  `MaxTerritoryRoyaltyPercent`, `MaxTerritoryTaxPerCycleCredits`, `MaxTerritoryZonesPerGovernment`,
  `MaxGovernments`, `TerritoryTaxIntervalMinutes`, `GovernancePollSeconds`, `EnableCitizenStipend`,
  `StipendIntervalMinutes`, `MaxStipendPerCitizenCredits`, `MaxStipendRecipientsPerCycle`,
  `EnableGovernmentCharters`, `GovernmentCharterFeeCredits`, `EnableElections`,
  `ElectionDefaultDurationMinutes`, `EnableGovernmentBonds`, `BondCouponIntervalMinutes`,
  `MaxBondFaceValueCredits`, `MaxBondCouponPercent`, `EnableCentralBankAprOverride`.
- Verified: Release build succeeds with 0 warnings / 0 errors against the Torch/SE reference
  assemblies. The in-game `!econadmin boundarytest` and live self-tests still need to be run on a
  running Torch server.

## v1.5.0-alpha - Government Taxation

- Adds **government taxation**: a server designates one faction as the government
  (`GovernmentFactionTag`) with an Econ+ faction treasury, and every `GovernmentTaxIntervalMinutes`
  (default ~30 days) each eligible player is assessed a flat `GovernmentTaxPerPlayerCredits` into
  that treasury. Collection runs through the new authoritative
  `EconomyExpansionService.TryCollectToNamedAccount` path (player debit -> treasury credit, atomic,
  restart-recoverable, refund-on-failure) and never calls native Keen banking. Off by default.
- **The taxman:** unpaid tax accrues as arrears; after `GovernmentTaxGracePeriodMinutes` a
  `GovernmentTaxLateFeePercent` late fee is added per cycle and Econ+ keeps auto-collecting up to
  `GovernmentTaxMaxCollectedPerCycleCredits` per cycle (never negative), with an in-game reminder
  each cycle. At `GovernmentTaxExtremeDebtCredits` the player's economy privileges are suspended
  (transfers and loan applications blocked until paid) and they are flagged for staff. Assets are
  never seized (Hangar+ owns grids); enforcement is purely economic.
- New per-player state store (`EconomyGovernmentStore` / `EconPlusGovernment.xml`) tracks arrears,
  lifetime assessed/paid, missed cycles, suspension, and the assessment schedule; enabling the tax
  seeds the schedule without an immediate surprise assessment.
- Commands: `!econ tax`, `!econ tax pay [amount]`; `!econadmin tax`, `!econadmin tax run`,
  `!econadmin tax status <steam-id>`, `!econadmin tax forgive <steam-id> CONFIRM`.
- New config: `EnableGovernmentTax`, `GovernmentFactionTag`, `GovernmentTaxPerPlayerCredits`,
  `GovernmentTaxIntervalMinutes`, `GovernmentTaxGracePeriodMinutes`, `GovernmentTaxLateFeePercent`,
  `GovernmentTaxMaxCollectedPerCycleCredits`, `GovernmentTaxExtremeDebtCredits`,
  `GovernmentTaxSuspendPrivilegesOnExtremeDebt`, `GovernmentTaxFlagAdminsOnExtremeDebt`,
  `GovernmentTaxExemptSteamIds`, `GovernmentTaxPollSeconds` (all with backward-compatible defaults).
- The Keen boundary is preserved (the new government service references no native banking type).
  Verified: Release build succeeds with 0 warnings / 0 errors against the Torch/SE reference
  assemblies. The in-game `!econadmin boundarytest` and live self-tests still need to be run on a
  running Torch server.

## v1.4.0-alpha - Faction Payroll

- Adds leader-run **faction payroll**: an authorized faction founder or leader pays every eligible
  faction member a flat amount from the faction's Econ+ treasury with `!econ payroll
  <credits-per-member> ["purpose"]`, and can dry-run it first with `!econ payroll preview
  <credits-per-member>`. Authorization is verified on the server against live Space Engineers
  faction state (founder, or leader-ranked officer unless `FactionPayrollFoundersOnly`); a
  client/command-UI check is never trusted.
- Money comes only from the faction's admin-created `Faction` treasury (tag-matched) and moves
  entirely through the authoritative Econ+ accounting layer (`EconomyExpansionService` ->
  `EconomyExpansionStore`/`EconomyBalanceService`); payroll never calls native Keen banking and
  never creates credits. Recipients are the faction's current members with a resolvable Steam ID64
  (offline included); the initiator is paid as a member by default (`FactionPayrollIncludesInitiator`).
- Insufficient funds abort the whole run before any credits move (validated against treasury balance
  and daily spending limit), reporting required vs available. Per-member amount must be positive and
  within `MaximumFactionPayrollCreditsPerMember`; member count is bounded by
  `MaximumFactionPayrollRecipients`; totals use checked arithmetic; a per-initiator cooldown
  (`FactionPayrollCooldownSeconds`) guards against duplicate runs; concurrent payroll against one
  treasury is serialized.
- Centralizes named/faction-account payouts into a single atomic, restart-recoverable
  `TryDisburseFromNamedAccount` path (debit account -> credit member, with the account durably
  restored if a member credit fails, `RecoveryRequired` retained on any unconfirmed reversal).
  `TryPayNamedAccount` now uses this path, gaining refund-on-failure recovery.
- Anti-fraud auditing: every run is stored as a durable `EconomyPayrollRecord` (initiator Steam ID64,
  faction, treasury, per-member amount, recipient/paid counts, totals), each member payment is a
  ledger transaction tagged with the initiator Steam ID, and admins review runs with
  `!econadmin payrolls [count]`.
- New config: `EnableFactionPayroll`, `FactionPayrollFoundersOnly`, `FactionPayrollIncludesInitiator`,
  `FactionPayrollCooldownSeconds`, `MaximumFactionPayrollRecipients`,
  `MaximumFactionPayrollCreditsPerMember` (all with backward-compatible defaults). This is separate
  from the admin-scheduled `EnablePayroll` program engine, which is unchanged.
- The Keen boundary is preserved: the new `EconomyFactionService` only reads faction/world state and
  references no money type. Verified: Release build succeeds with 0 warnings / 0 errors against the
  Torch/SE reference assemblies. The in-game `!econadmin boundarytest` and live self-tests still need
  to be run on a running Torch server.

## v1.3.2-alpha - LCD Styling Fix and Setup Documentation

- LCD panels are now styled once (monospace font, `LcdFontSize`, colours) instead of on every
  refresh, so a font or size an admin sets by hand is no longer reset each update. New config
  `LcdFontSize` (default 0.7) tunes the size, and `Style=false` in a panel's Custom Data opts a
  panel out of Econ+ styling entirely.
- Station and Exchange boards tighten to 12 rows and keep the news headline to a single line so
  content fits standard panels.
- README gains a full setup guide: the item catalog, a category/item reference (ores, ingots,
  components, ammo, tools, bottles, and how symbols and MarketCatalog.csv work), a note that there
  are no ship/station item types (a depot is a named grid), depot presets, and the LCD styling
  controls.
- Verified: Release build zero warnings/errors; boundary 2/2; escrow 21/21; market 18/18;
  invest 15/15.

## v1.3.1-alpha - Player-to-Player Shops

- Adds a player marketplace: sellers list their own goods for other players to buy, on top of the
  NPC market. When physical delivery is on, the seller's real items are reserved from inventory at
  listing time; otherwise a virtual commodity holding is reserved. Peer trades do not move the NPC
  market price.
- Purchases can be partial, settle buyer -> seller through the authoritative accounts with an
  optional treasury fee (credits conserved), and deliver the goods to the buyer. Ordering prevents
  duplication before loss: quantity is claimed from the listing before the buyer is charged, and a
  failed delivery refunds the buyer and restores the listing.
- Commands: `!econ shop [page]`, `!econ shop sell <sym> <qty> <price>`, `!econ shop buy <id> <qty>`,
  `!econ shop mine`, `!econ shop cancel <id>`. Adds `EconomyShopStore`/`EconomyShopService`,
  per-seller listing caps, an optional expiry, and configuration.
- Completes the eight requested player-experience features. Verified: Release build zero
  warnings/errors; boundary 2/2; escrow 21/21; market 18/18; invest 15/15.

## v1.3.0-alpha - Player-Experience Release (Custom Currency and HUD)

Rolls up the player-experience expansion (price trends and P&L, alerts and limit orders, market
events/news, leaderboards, dividends and savings interest) and adds:

- Configurable currency: `CurrencyName` and `CurrencySymbol` rename "credits"/"cr" across every
  player-facing amount in commands, LCD panels, and messages.
- Corner HUD notifications for asynchronous notices (payments, trade fills, dividends, alerts,
  loan reminders) alongside chat, via the visual-script notification API (`EnableHudNotifications`,
  default on). Resolving the runtime identity is a world read, not native banking.
- Verified: Release build zero warnings/errors; boundary 2/2; escrow 21/21; market 18/18;
  invest 15/15.

## v1.2.6-alpha - Dividends and Savings Interest

- Adds optional exchange dividends (`EnableDividends`, default off): each instrument can pay a
  per-share dividend, credited to shareholders from the treasury once per configured interval,
  idempotent per interval window and bounded per pass. Default instruments carry a small dividend.
- Adds optional savings interest (`EnableSavingsInterest`, default off): savings named accounts
  accrue interest from the treasury, prorated from the annual rate over the configured interval.
- Both are treasury-funded, so credits stay conserved, and both run on the existing scheduled pass
  through `EconomyBalanceService`/treasury payouts - the Keen boundary is unaffected.
- Verified: Release build zero warnings/errors; boundary 2/2; escrow 21/21; market 18/18;
  invest 15/15.

## v1.2.5-alpha - Market Events, News, and Leaderboards

- Adds periodic market events: with a configurable chance a commodity gets a supply shock
  (shortage pushes its price up, surplus pushes it down), a headline is broadcast in chat, and the
  current headline scrolls on the `Station` and `Exchange` LCD panels. Events revert naturally
  through the existing price decay. Only market pressure and Torch chat are used, so the Keen
  boundary is unaffected.
- Adds leaderboards: `!econ top [n]` lists the wealthiest players, and `!econ movers` lists the
  biggest 24h gainers and losers on the commodity market.
- Adds `EconomyMarketEventService`, a chat broadcast channel, and configuration for event
  frequency/magnitude and leaderboards.
- Verified: Release build zero warnings/errors; boundary 2/2; escrow 21/21; market 18/18;
  invest 15/15.

## v1.2.4-alpha - Price Alerts and Limit Orders

- Adds price alerts: `!econ alert <sym> gt|lt <price>` DMs you once when a commodity or instrument
  crosses the threshold; `!econ alerts` lists them and `!econ alert cancel <id>` removes one.
- Adds standing limit orders: `!econ order buy|sell <sym> <qty> <price>` auto-fills a buy when the
  price falls to the limit or a sell when it rises to the limit; `!econ orders` and
  `!econ order cancel <id>` manage them.
- A symbol is routed to the commodity market or the investment exchange automatically. Orders and
  alerts are processed on a timer on the game thread and settle through the same market/exchange
  services as manual trades, so the Keen boundary is unaffected. A limit buy that cannot yet fund
  or deliver stays pending and retries.
- Adds `EconomyOrdersStore`/`EconomyOrderService`, per-player caps, and configuration.
- Verified: Release build zero warnings/errors; boundary 2/2; escrow 21/21; market 18/18;
  invest 15/15.

## v1.2.3-alpha - Price Trends and Portfolio P&L

- Commodities now show a 24h price change: each commodity keeps a daily reference (open) price
  that rolls once a day, and the change appears on `!econ trade`, `!econ trade quote`, and the
  `Station` LCD (a new change column), matching the exchange's existing change display.
- Holdings and portfolios now track cost basis: buys update a weighted average cost, sells leave
  it unchanged, and `!econ trade holdings` / `!econ invest portfolio` show per-line and total
  unrealised profit/loss so players can see whether they are up or down.
- Verified: Release build zero warnings/errors; boundary 2/2; escrow 21/21; market 18/18;
  invest 15/15 (new average-cost checks).

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
