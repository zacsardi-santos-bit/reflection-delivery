## Description

When a user views the list of people an account follows, or the list of an account's followers, the response includes basic profile information for each listed account but does not include any relationship status from the viewer's perspective. Specifically, there is no indication of whether the viewing user already follows a listed account, or whether they have blocked one.

This means client applications have no way to render relationship indicators (such as a "Following" badge or a blocked-user marker) for the accounts shown in these lists. The data simply isn't returned.

## Expected Behavior

- Each account returned in a following or followers list should include a field indicating whether the current viewer follows that account.
- Each account returned should also include a field indicating whether the current viewer has blocked that account.
- These relationship flags should work correctly for both locally-stored accounts and for accounts retrieved remotely from external ActivityPub servers.
- For remote accounts that are not found in the local database, both flags should default to false.

## Why This Matters

Without these fields, clients cannot accurately display the viewer's relationship with accounts in follower/following lists. A user may be viewing a follower list and have no way to know which accounts they already follow back or have blocked — making it impossible to build useful UI affordances around these states.
