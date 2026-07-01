I'm working on the Amazon Verified Permissions integration in Airflow's AWS authentication manager, and there are a few things that need to be fixed or added.

First, authorization for DAG access is not actually going through the policy service — right now it just checks if the user is logged in. I need it to go through the real authorization check, the same way other resources like variables and connections do. It also needs to support passing context about which specific aspect of a DAG is being accessed (for example, its code or its run history), so the policies can make more fine-grained decisions.

Second, the authorization facade currently doesn't handle the case where the user object is None — instead of returning "not authorized," it may error. It should return false cleanly in that situation.

Third, the facade needs to support optional context that is passed through to the policy evaluation. Previously there was an entity-fetcher mechanism that is no longer needed; it should be replaced by a simpler context dictionary. When context is provided, it should be wrapped in the format the policy service expects. When context is absent, it should be omitted from the request rather than passed as null.

Finally, there needs to be a constant for a region name configuration key in the auth manager constants module, with the value "region_name", so the authorization service can be configured to use a specific AWS region.

All the authorization methods should also return the actual boolean decision from the policy service, not just call it and ignore the result.
