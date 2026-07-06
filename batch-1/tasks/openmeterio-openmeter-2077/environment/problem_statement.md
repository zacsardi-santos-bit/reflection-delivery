## Description

When a subscription item's pricing or configuration is changed on a running subscription, the billing system doesn't properly update the invoices already in flight. Gathering invoices continue to hold the old, now-incorrect billing lines rather than reflecting the pro-rated amounts for the period the old configuration was active and the new lines for the updated configuration. Draft invoices retain stale period data and incorrect usage quantities. If an invoice has already been finalized, there is no mechanism to signal that the system cannot retroactively correct it — the issue is silently ignored rather than surfaced as a warning. Additionally, draft invoices that become empty after subscription changes are not cleaned up.

## Expected Behavior

- When a subscription item is updated, gathering invoices should be reconciled to show pro-rated amounts for the old configuration (or nothing if the pro-rated amount is zero) and new billing lines for the updated configuration starting from the time of change.
- Draft invoices that are affected by subscription changes should have their billing periods and usage quantities updated to match the new subscription state.
- When a subscription change would require retroactively modifying a finalized invoice, the finalized invoice should remain unchanged, but a warning should be attached to it indicating which lines could not be updated.
- Draft invoices that have no remaining lines after subscription changes should be automatically deleted rather than left in a broken state.

## Why This Matters

Subscription items may need to be changed mid-cycle for various reasons (plan upgrades, pricing corrections, phase additions). Without correct reconciliation, customers can be overbilled or underbilled. Without a warning mechanism for finalized invoices, billing discrepancies go undetected. Without cleanup of empty draft invoices, the invoice list becomes polluted with meaningless records.
