## Description

We need to add support for creating orders from mobile in-app purchase receipts for the Leo premium AI assistant product. Currently, the system has no way to translate mobile-specific subscription identifiers (from Android and iOS app stores) into internal product configurations, and there is no structured flow for handling the full receipt-to-order process with proper error handling.

## Expected Behavior

- Mobile subscription identifiers from Android (various build flavors: release, beta, nightly) and iOS should be correctly mapped to internal product SKUs for both monthly and yearly billing cycles.
- The system should return a well-defined error when an unrecognized subscription identifier is submitted.
- Order item configurations (pricing, credential duration, issuance settings) should vary correctly depending on the deployment environment (production, staging, and development/local).
- Payment processor redirect URIs for success and cancellation should also be environment-specific.
- Receipt request data should be properly validated, with clear per-field validation errors for missing required fields or invalid vendor values.
- Purchase-specific error states (failed, pending, deferred, unknown status) should map to distinct, structured error codes that clients can handle programmatically.
- When creating an order from a receipt, errors from any step of the process should be propagated appropriately.
- Order metadata for mobile purchases should record the external ID, payment processor, and vendor, distinguishing between Android and iOS origins.
- When building a new order, a zero total price should automatically set the order status to paid; a single-item order should inherit the item's location; a multi-item order should have no location set; the validity duration should be taken from the first item that specifies one.

## Why This Matters

Without these utilities, mobile users who purchase Leo premium subscriptions through the app store cannot have their purchases recognized and provisioned. This is the foundational layer that connects mobile store purchase receipts to the subscription fulfillment system.
