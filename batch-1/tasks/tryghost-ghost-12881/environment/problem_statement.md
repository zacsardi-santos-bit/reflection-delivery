## Description

Ghost's membership system tracks which Stripe subscription a member is on, but there is currently no direct link between a member's subscription record and the specific price entry in the newer Stripe billing model. The subscription table only carries a legacy plan identifier, with no dedicated field pointing to the corresponding Stripe price. This makes it impossible to reliably associate subscriptions with the structured product-and-price hierarchy that Stripe uses.

## Expected Behavior

- A subscription record should carry a dedicated field for the Stripe price identifier, in addition to the existing plan identifier field.
- When creating a subscription, it should be possible to provide a Stripe price identifier that references an existing price record.
- The system should support a hierarchy of products → Stripe products → Stripe prices, and subscriptions should be linked to this hierarchy through the price identifier.
- The foreign key relationships in the schema between these entities should not cascade deletions, so that removing a parent record does not automatically destroy child records.
- The database schema integrity check should reflect the updated schema.

## Why This Matters

Without a direct link to Stripe price records, the system cannot accurately track which specific price a member subscribed to. This prevents proper billing data tracking and blocks future work to display, filter, or update subscription pricing information based on the Stripe pricing model.
