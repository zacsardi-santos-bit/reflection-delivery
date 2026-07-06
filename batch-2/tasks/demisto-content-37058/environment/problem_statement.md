## Description

The Cybereason integration has two important gaps that need to be addressed: session token management is not being validated before use, and the malop-to-incident conversion is incomplete.

When the integration makes API calls using a cached session token, it never checks whether that token has expired. This means requests can silently fail after a period of inactivity without automatically refreshing the authentication. A proper session check should verify the token's expiry time and, if expired, re-authenticate and update the session before proceeding.

Additionally, the function that converts a Cybereason malop into a platform incident is returning very little information — currently only the incident name. It should be producing a full incident record that includes the incident status (mapped from the malop's management status), timestamps for creation and last update, detection type, EDR flag, root cause element name and type, and a mirror ID. These fields are needed for downstream automation and incident management workflows.

## Expected Behavior

- When a cached session token is expired, re-authenticate automatically and store the refreshed token along with its new expiry time (current time plus 28000 seconds).
- The malop-to-incident conversion should return a fully populated incident record with all relevant fields populated from both EDR malops (which use nested field structures) and non-EDR malops (which use flat field structures).
- The status of the malop should be translated to a numeric incident status: active/unread/reopened malops should be status 0, remediated malops should be status 1, and resolved malops should be status 2.
- If the malop data is not in the expected format, an appropriate error should be raised.

## Why This Matters

Without session refresh logic, long-running integrations will experience authentication failures. Without complete incident data, analysts cannot see critical context about threats directly in the incident view, reducing the effectiveness of automated triage workflows.
