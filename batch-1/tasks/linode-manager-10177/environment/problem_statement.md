## Description

Several parts of the Linode manager application incorrectly treat a price of $0 as if it were a missing or unknown price. This is a problem because some regions legitimately offer services at no cost — for example, backups in certain regions may be free. When a user in one of these regions tries to enable backups, the dialog shows a pricing error message and disables the "Enable Backups" button, even though the price is simply $0. Similarly, the "Enable All Backups" drawer displays the wrong total cost (excluding free-tier Linodes from the sum), and the Linode migration pricing panel does not display at all when the destination region has $0 pricing. The Kubernetes node pool resize and add drawers also show erroneous price-unavailable notices in $0 regions.

## Expected Behavior

- When a region's pricing is legitimately $0, the UI should display "$0.00" rather than a price-unavailable placeholder or an error indicator.
- The "Enable Backups" button should remain enabled when the backup price is $0.
- No error notice should appear when the price is $0; error states should only appear when the price genuinely cannot be loaded.
- Total backup prices should correctly include Linodes in $0-priced regions, contributing $0 to the sum rather than being excluded.
- Pricing panels in the migration and node pool flows should render when prices are $0, displaying the zero values correctly.
- The "Enable All Backups" drawer should correctly count and include all Linodes without backups, including those in $0-priced regions.

## Why This Matters

Users in regions with $0 pricing are currently blocked from performing basic actions like enabling backups and resizing node pools due to false error states. This is confusing because there is no actual error — the service is simply free in their region. Fixing this distinction between "price is $0" and "price is unavailable" will unblock these users and ensure pricing information is displayed accurately across the application.
