I'm doing a patch release across a bunch of Home Assistant integrations and need help knocking out several unrelated bugs, they're independent so treat each on its own.

First up the audio MIME type parser in the AI conversation integration only handles uppercase type codes, so when a device advertises its format with lowercase chars (like lowercase "l" instead of uppercase "L" in the audio subtype) it rejects it or botches the param extraction. I want it working identically for both cases regardless of letter case.

Also there's a security hole: users configured as local-only (meant to be restricted to the home network) can bypass that by using signed URLs or WebSocket connections from the internet, neither path enforces the local-network check right now. Inactive user accounts also aren't rejected when hitting signed URLs, oh and the WebSocket rejection should send back a clear explanation message saying why remote access was denied.

On IMAP, when it loses connection to the mail server during a push-mode idle session it logs nothing about canceling the pending idle wait, I want a diagnostic message that includes the server name so admins can actually diagnose it.

Then the MQTT light bug: an optimistic light saved in the off state crashes when you turn it on after a restart, because the saved state has a null color mode and the restore logic blindly overwrites the properly initialized default. After the fix the light should accept turn-on and report a valid color mode, so don't let restore clobber an initialized color mode with null.

Roborock vacuums raise a cryptic internal error on an invalid fan speed but only for standard and Q7 series (the Q10 already raises a proper validation error), I want all robot types raising a clear validation error that names the specific invalid value.

Victron Energy Bluetooth: some devices send advertisement packets with mode bytes the parser doesn't understand and right now those count as key failures, eventually triggering unnecessary reauth even when the encryption key is fine. These unrecognized-mode advertisements should be totally neutral, not incrementing or resetting the failure counter either direction. Just to be clear, two bad advertisements, then one unrecognized, then one more bad should still trigger reauth (three consecutive failures).

Finally the Gardena Bluetooth integration needs tiny sensor metadata fixes: "Current distance" and "Current flow" should get a measurement state class, and "Overall flow" should use the water device class instead of the generic volume one.
