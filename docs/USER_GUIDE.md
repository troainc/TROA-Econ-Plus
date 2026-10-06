# Econ+ — server owner and player guide

Econ+ is a Torch-side economy service. It owns its accounts, durable transactions, commands, and optional Discord notices. Players use it in game; no separate client mod is needed. Start with the [owner setup guide](SERVER-OWNER-SETUP.md), then review [configuration](CONFIGURATION.md), [commands](COMMANDS.md), [player instructions](PLAYER-GUIDE.md), and [troubleshooting/data safety](TROUBLESHOOTING.md). Check the package version before enabling options because configuration support follows the installed build.

## What players can do

The enabled Econ+ command set can expose balances and account details, transfer credits, access server shops and player markets, list and fulfill orders, create and follow contracts, use scheduled payments, and participate in the server's configured financial systems. Depending on the release and server configuration, that may include investments, insurance, government/tax policy, governance, treasury features, auctions or market events, and LCD views. `!econ help` on the actual server is the definitive list of registered commands. Econ+ can be integrated with other TROA services, but those integrations are optional and should be enabled only when both sides are installed/configured.

## Server-owner setup

1. Install the matching release package and start Torch once so Econ+ creates its config and data files.
2. Back up the generated data directory and choose the starting balance, currency presentation, transfer limits/fees, and feature toggles deliberately.
3. Review all payment, shop, contract, market, insurance, and governance settings in the configuration guide; do not assume that an unused subsystem is active by default.
4. If Discord/webhook delivery or another plugin integration is used, configure Econ+'s own endpoint and test it independently.
5. Test player and admin flows with disposable accounts on a test server before importing balances or opening a live market.

## Ledger and data safety

Economy state is persisted by Econ+'s stores and ledger. Treat the full data folder as one backup unit and preserve it during plugin updates. Before manually editing data, stop the server and retain a restorable copy. Avoid promising rollback for already-paid external effects; verify transactions, contract escrow, scheduled payments, and integration events in logs after upgrades.

## Commands, permissions, and integrations

Use `!econ` for the player command family and the documented admin root for privileged actions; the command reference gives exact syntax. Keep grants, treasury/policy changes, and moderation of market content restricted to server staff. Monitor+ can relay eligible commands but does not own Econ+ command registration or webhooks. Optional Admin Overseer rewards remain a distinct system; do not configure the same award path twice.
