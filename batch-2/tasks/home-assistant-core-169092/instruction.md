I'm working on a patch release for Home Assistant and need help fixing several independent bugs across different integrations.

*   The _parse_audio_mime_type function must accept both uppercase ('audio/L16') and lowercase ('audio/l16') variants of the audio subtype prefix. Input 'audio/L16;rate=24000' must return {'bits_per_sample': 16, 'rate': 24000}. Input 'audio/l16; rate=24000; channels=1' must also return {'bits_per_sample': 16, 'rate': 24000} (channels is ignored).

*   The _parse_audio_mime_type function must raise HomeAssistantError for unsupported MIME types such as 'video/mp4'.

*   When a signed HTTP path is accessed by a user marked as local-only from a local IP address (e.g., 192.168.1.x), the request must succeed with HTTP 200 OK and include the user's ID in the JSON response.

*   When a signed HTTP path is accessed by a user marked as local-only from a remote (non-local) IP address, the request must be rejected with HTTP 401 Unauthorized for both GET and HEAD methods.

*   When a signed HTTP path is accessed by a user whose account is inactive (is_active=False), the request must be rejected with HTTP 401 Unauthorized.

*   When the IMAP push coordinator cancels a pending IDLE future (e.g., due to a lost connection), it must emit a debug log message containing the text 'Canceling IDLE wait for {server_hostname}', where {server_hostname} is the configured IMAP server address.

*   When an MQTT JSON-schema light restores state from a saved snapshot where color_mode was null (as happens when the light was off at save time), the restored color_mode must not overwrite the properly initialized default. After restoration, calling turn_on must succeed without raising an error about a missing color mode.

*   When an MQTT JSON-schema optimistic light is turned on after restoring from a null color_mode state, it must publish exactly '{"state":"ON"}' to the command topic, transition to the ON state, and report a non-null color_mode attribute.

*   When set_fan_speed is called on any Roborock vacuum entity (standard, Q7, or Q10 type) with an invalid fan speed value, it must raise ServiceValidationError. The error message must match the pattern 'Invalid fan speed: {fan_speed}', where {fan_speed} is the invalid value provided (e.g., 'Invalid fan speed: some-mode').

*   When a Victron BLE device broadcasts advertisements whose device type the parser does not recognize (i.e., unrecognized mode bytes), those advertisements must be treated as neutral — they must neither increment nor reset the consecutive authentication failure counter.

*   When only unrecognized-mode Victron BLE advertisements are received (even in quantities exceeding the reauthentication threshold), no reauthentication flow must be triggered.

*   In Victron BLE, the advertisement sequence bad → bad → unrecognized-mode → bad must still trigger reauthentication, because the unrecognized-mode advertisement does not reset the accumulated failure count (3 consecutive bad advertisements remain).

*   When a local-only user attempts WebSocket authentication from a remote IP address, the WebSocket response must have type TYPE_AUTH_INVALID with message exactly 'User cannot authenticate remotely', and the wrong login must be reported to the ban system.

*   When a local-only user attempts WebSocket authentication from a local IP address, the WebSocket response must have type TYPE_AUTH_OK.

*   The 'Current distance' Gardena Bluetooth sensor must expose state_class MEASUREMENT in its capabilities and state attributes.

*   The 'Current flow' Gardena Bluetooth sensor must expose state_class MEASUREMENT in its capabilities and state attributes.

*   The 'Overall flow' Gardena Bluetooth sensor must use device_class WATER (not VOLUME).


*   Interface details: Type: Function
Name: _parse_audio_mime_type
Location: homeassistant/components/google_generative_ai_conversation/helpers.py
Signature: _parse_audio_mime_type(mime_type: str) -> dict[str, int]
Description: Parses an audio MIME type string and extracts audio encoding parameters. Must handle both uppercase (e.g., "audio/L16") and lowercase (e.g., "audio/l16") variants of the subtype prefix. Returns a dict with integer fields "bits_per_sample" and "rate". Raises HomeAssistantError for unsupported MIME types (e.g., non-audio types like "video/mp4"). Additional parameters such as "channels" are accepted in the MIME type string but are ignored in the return value.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.