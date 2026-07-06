## Description

Ghost supports gift subscriptions, where one person can purchase a membership on behalf of another. However, these gift purchase events are currently not surfaced in the member activity feed, making it impossible for site operators to see this activity alongside other payment-related events. Additionally, when operators filter payment events in the activity feed, gift purchases are not included in that group, so they would not be toggled together with regular payments and donations.

## Expected Behavior

- Gift purchase events should appear in the member activity feed as a distinct event type, showing details such as the amount, currency, tier, cadence, duration, and the buyer's information.
- When the buyer member information is not available (e.g., an anonymous gift purchase), the member field should be absent.
- Sensitive internal fields (such as payment processor tokens, checkout session IDs, payment intent IDs, and status codes) should not be exposed in the activity feed event data.
- When an operator toggles payment-related events on or off in the activity feed, gift purchase events should be included in the same group as regular payment events and donation events, so they are all shown or hidden together.

## Why This Matters

Site operators rely on the member activity feed to understand member behavior and troubleshoot issues. Without gift purchase events appearing in the feed, a significant category of member activity is invisible. Grouping gift purchases with payment events also makes filtering more intuitive and consistent.
