## Description

Saleor supports two payment systems: a legacy payment flow and a newer transaction-based flow. Several gaps exist in how these two flows interact, leading to potential conflicts, missing information in order events, and incorrect flow selection.

## Problems to Fix

1. **Missing gateway info in order events**: When an order becomes fully paid, the system records an event, but the event contains no information about which payment gateway was used. Merchants and administrators lose traceability about how the payment was processed.

2. **Legacy payments not deactivated when switching to transaction flow**: When a checkout has existing legacy payment records and a transaction-based payment is then initialized or processed, those legacy payments remain active. This can cause conflicts between the two payment systems.

3. **No protection against concurrent checkout completion**: If a checkout completion is already underway, users can still trigger transaction-related payment operations concurrently. This can lead to race conditions. The system should reject such operations with a clear error indicating the checkout is locked.

4. **Incorrect flow selection for partially-authorized checkouts**: When a checkout is partially authorized and has both transaction items and active legacy payments, the system may not correctly decide which payment flow to use for completion.

## Expected Behavior

- The "order fully paid" event should record which payment gateway was responsible.
- Initializing or processing a transaction on a checkout with existing legacy payments should automatically deactivate those legacy payments.
- Attempting to initialize or process a transaction on a checkout that is already being completed should return a clear error indicating that checkout completion is in progress.
- A partially-authorized checkout with an active payment should complete via the legacy payment flow; one without an active payment (but with transaction items) should complete via the transaction flow.

## Why This Matters

These fixes prevent double-payment scenarios, improve audit trails for merchants, and eliminate race conditions during checkout completion when using the transaction-based payment flow.
