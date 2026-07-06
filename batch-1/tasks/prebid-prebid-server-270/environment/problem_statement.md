## Description

The prebid server does not currently validate DigiTrust consent signals that arrive in bid requests, nor does the Rubicon adapter pass DigiTrust identity data through to its downstream calls. DigiTrust is a privacy and identity protocol used in programmatic advertising: a user's preference value indicates whether they have consented to being identified. Without validation, requests containing an invalid or non-consenting DigiTrust signal are silently accepted and forwarded, which could violate user privacy preferences.

## Expected Behavior

- When a bid request contains a user object with a DigiTrust consent field indicating the user has opted out (non-zero preference value), the server should reject the request with an error before proceeding to the auction.
- When a bid request has no user, no user extension, or a DigiTrust preference value of zero (indicating consent), the request should be accepted normally.
- The Rubicon bidder adapter should forward any DigiTrust identity information present in the incoming bid request's user extension to Rubicon's endpoint, so that Rubicon can honor user privacy preferences appropriately.

## Why This Matters

Without this validation, bid requests from users who have opted out of DigiTrust identification could still be processed and their identity data forwarded to downstream partners. Adding validation ensures the server respects DigiTrust consent signals at the auction entry point, and passing the data through to Rubicon ensures the full privacy signal chain is preserved end-to-end.
