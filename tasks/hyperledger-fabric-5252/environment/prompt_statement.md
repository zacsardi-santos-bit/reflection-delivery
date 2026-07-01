I'm working on the ordering service's channel participation REST API. Right now the API lets you join a channel using a config block, remove a channel, or list channels — but there's no way to update an existing channel's configuration through this API. I need to add a channel update operation.

The update should accept a configuration update envelope as multipart form data submitted via a PUT request to the channels base URL. Before passing the envelope to the underlying channel manager, it should be validated to confirm it's a proper configuration update type and contains a valid channel ID. If validation fails, a 400 response with a descriptive message should be returned.

The channel manager interface also needs a corresponding update method that takes a channel ID and the envelope, and returns channel info on success.

For error handling, when the channel doesn't exist the response should be 405, when the orderer is a follower on that channel (not yet active) it should be 403, and when the channel is pending removal it should be 409. Bad request bodies, wrong content types, missing form fields, too many form parts, and oversized bodies should all return 400 with descriptive messages.

Finally, the allowed methods advertised in method-not-allowed responses for the channels base URL should be updated to include the new PUT method alongside the existing ones.
