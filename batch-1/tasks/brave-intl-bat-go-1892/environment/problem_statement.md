## Description

The Radom payment integration currently lacks proper input validation when creating checkout sessions, and the order metadata storage layer is missing support for 64-bit integer values. Both gaps result in unclear errors and missing functionality when processing Radom payments.

When a checkout session is initiated for a Radom payment, the system does not validate that the order has items, or that required metadata fields (redirect URLs and product identifiers) are present before contacting the payment processor. This means errors surface in unhelpful or opaque ways, making it difficult to diagnose configuration issues. Additionally, the method for creating Radom checkout sessions cannot accept an explicit expiry time, limiting testability and flexibility.

On the data storage side, there is no dedicated mechanism for storing 64-bit integer values in order metadata. This is necessary for persisting blockchain-specific data received from payment webhooks — such as chain identifiers and block numbers — which require the precision of a 64-bit integer type.

## Expected Behavior

- Creating a Radom checkout session should return a clear, specific error when the order has no items, when the success redirect URL is missing, when the cancel redirect URL is missing, or when the product identifier is missing from the order item metadata.
- A variant of the checkout session creation method should accept an explicit expiry time rather than always computing it from the current time, enabling time-controlled behavior.
- Client errors encountered while communicating with the payment processor should be propagated in a way that allows callers to identify their cause.
- On success, the checkout session response should include the session identifier returned by the payment processor.
- The order repository should support appending or updating a 64-bit integer value by key in an order's metadata, returning a recognizable error when the target order does not exist.

## Why This Matters

Without these changes, misconfigured Radom orders fail silently or with confusing error messages, and blockchain metadata from payment webhooks cannot be reliably stored with the correct numeric precision.
