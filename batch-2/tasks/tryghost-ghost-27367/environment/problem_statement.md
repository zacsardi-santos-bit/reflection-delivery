## Description

The gift subscription feature is missing the ability to actually redeem a gift. While the codebase can already check whether a gift is eligible for redemption (verifying it hasn't been redeemed, consumed, expired, or refunded, and that the recipient doesn't already have a paid subscription), there is no mechanism to perform the redemption itself. Recipients can receive a gift link, but nothing can convert it into an active subscription for their account.

## Expected Behavior

- A member should be able to redeem a gift by supplying their account ID and a gift token. This should run atomically so that two simultaneous redemption attempts cannot both succeed.
- When a gift is redeemed, the system should calculate the correct subscription end date based on the gift's billing period (monthly or yearly) and its duration, applied from the moment of redemption. Month-end date math should handle edge cases (e.g. adding a month to January 31 should overflow correctly into the following month rather than produce an invalid date).
- The repository layer should support persisting gift state changes (creating a new record or updating an existing one) and should support running operations inside a database transaction with row-level locking.
- The service layer should expose discrete, composable operations: fetching a gift by token (with a clear not-found error), asserting a gift is redeemable given a member's subscription status (with appropriate errors for each invalid state), and performing the full redemption transaction.
- The not-found error when a gift token does not exist should be consistent across all entry points.

## Error Conditions

- Attempting to redeem a non-existent gift token should produce a not-found error.
- Attempting to redeem a gift that has already been redeemed, consumed, expired, or refunded should each produce a distinct bad-request error with an appropriate message describing the specific reason.
- Attempting to redeem when the member already has an active paid or comped subscription should produce an error indicating they already have an active subscription.
- Attempting to redeem a gift on behalf of a member that does not exist should produce a not-found error identifying the missing member.

## Why This Matters

Without redemption, the gift subscription feature is incomplete — gifts can be purchased and sent, but never activated. Adding the redemption layer closes the loop and makes the feature functionally end-to-end.
