## Description

The current checkout flow for subscription products is tightly coupled to the subscriptions module, implemented as a "subscribe session" concept. This makes it harder to maintain, extend, and reason about, because checkout logic doesn't belong in the subscription service — it's a separate concern.

We need a new dedicated checkout module with its own endpoints, service, and schemas that cleanly encapsulates the process of creating and retrieving payment processor checkout sessions for product purchases.

## Expected Behavior

- A new API endpoint should be available for creating a checkout session, accepting a product price identifier and a success redirect URL. Providing an optional customer email should also be supported.
- The endpoint must validate inputs: the product and price must exist and not be archived, the redirect URL must be a valid URL, and any customer email must be in valid email format.
- If an authenticated user already has an active subscription to the product, creating a checkout should be rejected.
- For authenticated users (via cookie session), the user's existing payment processor customer ID should be associated with the checkout, and the user's ID should be included in the session metadata.
- For API token users who supply a customer email, the email should be forwarded to the payment processor.
- If the product has benefits that require tax collection, the checkout session must be created with tax collection enabled.
- When a user with an existing free subscription is upgrading to a paid tier, the existing subscription ID should be tracked in the checkout session metadata.
- A separate endpoint should allow retrieving a checkout session by ID, returning the session details including customer name and email when available.
- The old subscribe-session endpoints and service under the subscriptions module should be removed in favor of this new checkout module.

## Why This Matters

Centralizing checkout logic into a dedicated module improves code organization, reduces coupling between the checkout and subscription concerns, and makes it easier to extend the checkout flow in the future (e.g., supporting one-time purchases, additional payment methods, etc.).
