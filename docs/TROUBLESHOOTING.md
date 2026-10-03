# Troubleshooting and Data Safety

## Plugin does not load

- Confirm Torch is using the correct plugin package and a supported Space Engineers/Torch version.
- Check Torch logs for the first Econ+ error, not only later messages. Verify that the package contains the plugin files expected by its release instructions.
- Ensure configuration is valid XML and the server account can read/write Econ+'s storage folder.
- Compare the installed package version to the release notes. New settings in the current example do not make an older plugin build support those features.

## A command is unknown or denied

- Run `!econ help` or `!econadmin help` in game; these reflect the command modules actually loaded by your installed build.
- Check that the feature is enabled in configuration and that the caller has the required Torch rank or faction role.
- For a market trade, check the item symbol, station tag, distance, account balance, and available inventory space.
- Reload the config or restart Torch after changes, then check the response and server log.

## Prices or items are missing

- Run `!econadmin marketscan` and check the Torch startup/scan summary.
- Search by part of the item name with `!econ trade search <text>`.
- Check `MarketItemTypes`; a nonempty value restricts scanned categories.
- Use the exact symbol from `TROA-Econ-PlusData/MarketCatalog.csv`. A modded item must be registered as a physical item definition to appear.

## A market trade fails

- Check whether station proximity is required and whether a grid name contains `StationNameTag`.
- Check the player's credits, requested quantity, configured trade maximum, and inventory space.
- Review the Torch log around the transaction. Do not repeat a trade that returned an ambiguous error until its transaction state has been inspected.

## Discord notifications fail

- Confirm `EnableDiscordWebhook=true`, that the URL is a valid private HTTPS webhook, and that the named Discord endpoint exists.
- Keep the URL out of screenshots, issue posts, and support messages. Discord is notification-only: failed delivery does not reverse or complete an economy transaction.
- Use `!econadmin webhook` and `!econadmin webhook test` where supported.

## Transaction recovery and restore

Econ+ preserves ambiguous transactions for review; it does not guess whether a debit, credit, or refund succeeded. Before recovery:

1. Stop repeated attempts and enable maintenance mode if appropriate.
2. Back up the current world and full `TROA-Econ-PlusData` directory before changing anything.
3. Run `!econadmin recovery`, then inspect one record with `!econadmin recovery inspect <id>`.
4. Independently verify payer, recipient, treasury, native Keen, and consumer-plugin state as applicable.
5. Only then record a verified final state using the exact revision and an audit note. Recovery finalization records the decision; it does not move funds.

Do not delete the ledger, restore only one XML file from a different timestamp, or hand-edit active balance files. If a ledger is unreadable, preserve its corrupt backup and contact support with sanitized logs.

## What to include in a support report

Include the Econ+ package version, Torch/Space Engineers version, feature involved, command used, approximate UTC time, and the relevant sanitized log lines. Remove webhook URLs, tokens, player names/Steam IDs, and account data unless support specifically requests them through a private channel. See [SUPPORT.md](../SUPPORT.md).
