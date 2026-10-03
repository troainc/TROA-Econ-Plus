# TROA Econ+

TROA Econ+ is a server-side economy plugin for Space Engineers running on Torch. Players use chat commands to check balances, pay each other, trade items, and use the other economy features enabled by the server owner. There is no client mod or separate player app.

## Players: start here

Use these commands in game chat:

| What you want to do | Command |
|---|---|
| See the command list | `!econ help` |
| Check your Econ+ account | `!econ dashboard` |
| Check your Keen balance mirror | `!econ balance` |
| Pay another player | `!econ pay <steam-id> <credits>` |
| Pay a known offline player | `!econ payto <name-or-steam-id> <credits>` |
| See recent transactions | `!econ history <count>` |
| Browse the item market | `!econ trade` |
| Look up an item | `!econ trade search <name>` |
| Buy or sell at a trade station | `!econ trade buy <symbol> <quantity>` / `!econ trade sell <symbol> <quantity>` |
| Browse player listings | `!econ shop` |

The server owner controls which features are enabled. For worked examples and the rest of the player commands, see the [Player Guide](docs/PLAYER-GUIDE.md).

## Server owners: set up Econ+

Start with the [Server Owner Setup Guide](docs/SERVER-OWNER-SETUP.md). It walks through installation, first startup, configuring accounts and the market, testing, backups, and optional features. The [Configuration Guide](docs/CONFIGURATION.md) explains what to change and when; the complete public XML sample is [`TROA-Econ-Plus.cfg.example`](TROA-Econ-Plus.cfg.example).

The docs are organized as a small handbook. Start with the [Documentation Index](docs/README.md):

- [Server Owner Setup](docs/SERVER-OWNER-SETUP.md) — install and configure a working server economy.
- [Configuration Guide](docs/CONFIGURATION.md) — feature switches, defaults, and safe setup order.
- [Command Reference](docs/COMMANDS.md) — player and administrator commands with examples.
- [Player Guide](docs/PLAYER-GUIDE.md) — explain balances, payments, trading, accounts, loans, and other enabled features to players.
- [Troubleshooting and Data Safety](docs/TROUBLESHOOTING.md) — common problems, backup, recovery, and safe reporting.

## Downloads and compatibility

Use the plugin package supplied for the release you are installing, and keep its version matched to your server. This repository is the public documentation and configuration-example repository; it does not contain the closed-source plugin implementation. See [GitHub Releases](https://github.com/troainc/TROA-Econ-Plus/releases) for published packages, if available, and [CHANGELOG.md](CHANGELOG.md) for documented feature changes.

The current public configuration example documents the v1.8.0-alpha settings schema. A setting only works when the installed plugin build implements it. Econ+ runs on Torch for Space Engineers using .NET Framework 4.8, x64.

## Economy ownership

Econ+ is the authoritative owner of its credit accounts and transaction records. The optional Keen balance mirror is a compatibility feature; it does not make Keen banking the source of truth. Hangar+ owns ship/grid listings and custody, and can use Econ+ for safe settlement through the plugin API.

Read the [License](LICENSE.md) before using or redistributing the plugin. For help, use [SUPPORT.md](SUPPORT.md).

## Documentation

The README is the quick start. Use [`docs/README.md`](docs/README.md) to navigate the player guide, server-owner setup, configuration reference, commands, and troubleshooting.
