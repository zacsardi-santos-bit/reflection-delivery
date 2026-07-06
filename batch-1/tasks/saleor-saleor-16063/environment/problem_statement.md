## Description

There are several related issues around how Saleor handles payment flows during checkout completion and how payment-related order events are recorded.

**1. "Order Fully Paid" event lacks payment gateway information**

When an order is marked as fully paid after a payment is captured, the resulting order event contains no metadata about which payment gateway processed the payment. This makes it difficult to trace the payment origin in the order event log. The event should record the gateway identifier so operators and integrations can see which payment provider was used.

**2. Incorrect flow selection when both transactions and active payments coexist**

When a checkout has both transaction-based payment items and an active legacy payment, the checkout completion logic incorrectly routes to the transaction flow instead of the legacy payment flow. This can cause checkout failures when the customer intended to pay via the legacy payment method. The logic should prefer the legacy payment flow when an active payment is present, reserving the transaction flow for cases where no active payment exists.

**3. No protection against initializing/processing transactions on a locked checkout**

If a checkout is currently being completed (locked), it is still possible to call the transaction initialize or transaction process mutations on it. This can cause race conditions. These mutations should detect the locked state and immediately return a clear error instead of proceeding.

**4. Active legacy payments are not deactivated when a transaction-based payment is used**

When a transaction is initialized or processed on a checkout that still has active legacy payments, those payments remain active. This creates a risk of checkout completion proceeding through two different payment flows simultaneously. Active payments should be automatically deactivated as part of transaction initialization and processing.

## Expected Behavior

- The "order fully paid" event should include the payment gateway identifier in its metadata when a gateway is known.
- Checkout completion should use the legacy payment flow when an active payment exists alongside transaction items.
- Initializing or processing a transaction on a locked checkout should immediately return an error indicating the checkout is already being completed.
- Transaction initialization and processing on a checkout should atomically deactivate any pre-existing active legacy payments.

## Why This Matters

These fixes prevent double-processing of payments, improve audit trail visibility, and eliminate race conditions during checkout completion.
