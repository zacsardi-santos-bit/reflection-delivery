Implement a new channel update operation in the channel participation REST API to allow configuration updates to active channels. Ensure the operation accepts a configuration update envelope via multipart form data and validates the envelope before processing.

* Extend the ChannelManagement interface:
    * Add the `UpdateChannel` method with the signature: `UpdateChannel(channelID string, configUpdateEnvelope *cb.Envelope) (types.ChannelInfo, error)`.
    * Ensure it returns `ChannelInfo` on success and an error otherwise.
* Define and export constants and functions:
    * Export `FormDataConfigUpdateEnvelopeKey` as a string constant with the value "config-update-envelope".
    * Export `ValidateUpdateConfigEnvelope` function with the signature: `ValidateUpdateConfigEnvelope(env *cb.Envelope) (channelID string, err error)`.
        * Ensure it validates the envelope type and returns the channel ID or an error with the message "bad type" if invalid.
* Update the HTTP handler for PUT requests:
    * Accept PUT requests at `/participation/v1/channels` with `multipart/form-data` containing a single file part under the key 'config-update-envelope'.
    * Return HTTP 201 with a JSON body containing `ChannelInfo` on success.
    * Include PUT in the Allow header for 405 Method Not Allowed responses.
* Implement error handling for specific conditions:
    * Return HTTP 405 with "cannot update: channel does not exist" if the channel does not exist.
    * Return HTTP 403 with "cannot update: channel is not ready, he is a follower" if the orderer is a follower.
    * Return HTTP 409 with "cannot update: channel pending removal" if the channel is pending removal.
    * Return HTTP 400 with appropriate messages for malformed requests:
        * "cannot unmarshal file part config-update-envelope into an envelope" for non-parseable bytes.
        * "invalid config update envelope: bad header" for invalid envelope structure.
        * "unsupported Content-Type: [<value>]" for incorrect Content-Type.
        * "cannot read form from request body: multipart: boundary is empty" for missing multipart boundary.
        * "form does not contains part key: config-update-envelope" for missing form field key.
        * "form contains too many parts" for multiple form parts.
        * "cannot read form from request body: multipart: NextPart: http: request body too large" for oversized bodies.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.