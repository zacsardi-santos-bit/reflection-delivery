## Description

The channel participation REST API on the ordering service currently supports joining a channel from a config block, listing channels, and removing channels — but there is no way to submit a configuration update to an already-active channel through this API. Operators who need to update an existing channel's configuration (e.g. to add or remove orderer nodes, adjust policies, etc.) have no supported path through the participation API to do so.

## Expected Behavior

- The participation API should expose a new channel update operation that accepts a configuration update envelope via multipart form data.
- A validator should check that the submitted envelope is a valid channel configuration update (correct header type, non-empty channel ID) and return a meaningful error if it is not.
- The update operation should return the updated channel information on success.
- The following error conditions should be handled with appropriate HTTP status codes:
  - The specified channel does not exist → 405
  - The orderer is currently a follower on that channel and not yet active → 403
  - The channel is pending removal → 409
  - The request body is malformed or exceeds the allowed size → 400
- The list of allowed HTTP methods advertised in error responses for the channels base URL must include the new update method.

## Why This Matters

Without this operation, operators cannot push configuration changes to existing channels via the channel participation API, limiting the API's usefulness for ongoing channel management on the ordering service.
