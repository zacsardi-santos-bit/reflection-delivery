Implement a solution to correctly handle $0 pricing in the Linode manager application, ensuring that $0 is treated as a valid price rather than an error. Update the relevant components and functions to distinguish between a price of $0 and unavailable pricing data.

*   Treat a price of $0 as valid across the application, ensuring it does not trigger error states or disable actions.
*   Update `hasInvalidNodePoolPrice` function:
    *   Accept `pricePerNode` and `totalPrice` as arguments (number, null, or undefined).
    *   Return `false` when both arguments are 0.
    *   Return `true` when either argument is `null` or `undefined`.
*   Modify `BackupLinodeRow` component:
    *   Display '$0.00/mo' when backup price is $0.
    *   Do not render error indicator unless price is `null` or `undefined`.
*   Update `getTotalBackupsPrice` function:
    *   Include Linodes with $0 backup price in total calculation.
    *   Example: 2 Linodes at $2.50/month and 1 Linode at $0/month should total $5.00.
*   Adjust `MigrationPricing` component:
    *   Render container (data-testid='migration-pricing') when `hourly` and `monthly` are valid numbers, including 0.
    *   Display '$0.000' for hourly and '$0.00' for monthly when both are 0.
    *   Show backup price section with '$0.00' when `backups` prop has hourly and monthly as 0.
    *   Do not render when `hourly`, `monthly`, or `backups` is `null` or `undefined`.
*   Update `EnableBackupsDialog` component:
    *   Display '$0.00' when backup price is $0.
    *   Do not show `PRICES_RELOAD_ERROR_NOTICE_TEXT` or disable confirm button when price is $0.
    *   Show error notice and disable confirm button when price is unavailable.
*   Modify `BackupDrawer` component:
    *   Display correct total backup price, including $0 contributions.
    *   Show '$0.00' when all Linodes have $0 prices.
    *   Display '$--.--' when price data is unavailable.
    *   List only Linodes without backups enabled.
*   Export `typeFactory` from `src/factories/index.ts` for test accessibility.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.