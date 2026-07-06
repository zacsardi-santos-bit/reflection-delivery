## Description

The adaptive authentication scripting engine does not currently allow JavaScript-based authentication scripts to read or manipulate user claims during the authentication flow. Scripts can already make decisions about which authentication steps to execute, but they have no way to inspect the authenticated user's attributes (such as given name or last name) or to derive and attach new computed claims (such as a display name) back to the user's session.

## Expected Behavior

- Authentication scripts should be able to read user claims by their local or remote URI through an array-like claims interface on the authenticated user object.
- Scripts should be able to modify the value of an existing claim.
- Scripts should be able to construct a new claim object (specifying local URI, remote URI, and value) and push it into the user's attribute set, making the claim available for downstream processing.
- The current authentication subject (not just the last authenticated user) should be accessible from the script context, so that claims can be read and written against the primary subject of the authentication session.
- Claims pushed by a script must persist in the user's attributes after the authentication flow completes.

## Why This Matters

Without this capability, adaptive authentication scripts cannot perform attribute transformations — a common requirement when applications need derived or enriched user attributes (e.g., a display name built from first and last name) that are not stored directly in the identity store. Enabling claim manipulation within scripts removes the need for separate post-processing steps and makes the adaptive authentication model significantly more powerful.
