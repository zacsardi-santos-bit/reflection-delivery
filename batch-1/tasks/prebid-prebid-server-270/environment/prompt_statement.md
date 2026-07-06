I'm working on adding DigiTrust support to our prebid server. DigiTrust is a privacy/identity consent protocol used in programmatic advertising — users have a preference value that indicates whether they've consented to being tracked/identified.

Right now, the auction endpoint doesn't validate DigiTrust consent signals at all. If a bid request comes in with a DigiTrust object where the user's preference indicates they've opted out (a non-zero preference value), the server just passes it through without raising an error. We need to add validation so that requests with invalid DigiTrust consent (non-zero preference) are rejected, while requests with no DigiTrust data or a valid consenting preference (zero) are accepted without error.

Additionally, the Rubicon bidder adapter doesn't currently pass DigiTrust identity data from incoming bid requests to its outgoing calls. When a bid request contains a DigiTrust object in the user extension, the adapter should forward that data to Rubicon so Rubicon can properly handle user privacy preferences.

Can you implement both of these: the user validation logic in the auction endpoint, and the DigiTrust pass-through in the Rubicon adapter?
