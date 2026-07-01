## Description

Users currently have no way to see which API tokens are active on their account. There is no endpoint that returns a list of the tokens a user has, which makes it impossible to audit what sessions or credentials are currently authorized. On top of that, the internal function used to look up a token by its hash value has a generic name that doesn't communicate what field it uses for the lookup, which is confusing now that a lookup-by-ID variant is also needed.

## Expected Behavior

- An authenticated user should be able to retrieve a list of their currently active tokens through the API.
- Each token in the response should include its type, indicating whether it is a web session token, a device token, or a personal access token.
- A new personal access token type should exist alongside the existing web and device types.
- The database function that finds a token by hash should be named in a way that makes it clear the lookup is performed using the token hash (not an ID or another field).

## Why This Matters

Without a token listing endpoint, users have no visibility into their active credentials. Without the renamed lookup function, the codebase is ambiguous about how lookups work, making it harder to add sibling lookup methods (e.g., by token ID). Adding both clarifies the internal API and enables user-facing token management.
