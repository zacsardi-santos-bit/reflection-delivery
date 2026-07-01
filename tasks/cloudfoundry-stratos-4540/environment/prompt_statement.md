I'm working on adding configurable access control to the API key feature in our backend. Right now API keys either work or they don't — there's no way to restrict who can use them. I'd like to add a configuration setting with three modes: fully disabled, admin users only, and all users.

When the feature is disabled, any request to create, list, or delete API keys should be rejected with an appropriate "forbidden" response. When restricted to admins, non-admin users should be blocked from managing keys, and the middleware that authenticates requests using API keys should silently skip authentication for keys belonging to non-admin users (rather than granting access). When enabled for all users, existing behavior should be unchanged.

The session verification endpoint should also return the current setting as part of its configuration payload so the frontend can react appropriately.

The middleware that processes incoming requests with API key tokens needs to be updated as well: if the feature is fully disabled, the middleware should bypass all key processing; if it's admin-only, the middleware should look up the user associated with the key and only proceed if they are an admin. If any lookup fails or the user isn't allowed, the middleware should pass the request on without setting any authentication context — it should not fail the request outright.
