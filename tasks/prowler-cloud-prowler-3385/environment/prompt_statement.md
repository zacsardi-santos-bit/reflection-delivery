I'm working on adding Azure MySQL Flexible Server support to our cloud security scanning tool. Right now we have no checks at all for MySQL Flexible Servers on Azure — we're missing coverage for some pretty important security controls.

Specifically, I need to implement the underlying service layer to fetch MySQL Flexible Server instances and their configuration settings from Azure, as well as four new security checks:

1. A check that confirms audit logging is enabled on each server.
2. A check that confirms connection events are being captured in the audit log.
3. A check that confirms the server requires encrypted connections and won't accept unencrypted ones.
4. A check that confirms only sufficiently modern encryption protocol versions are permitted — servers allowing outdated versions should be flagged.

Each check should produce a pass or fail result for each server, with a human-readable message that identifies the server and subscription. If a server has no relevant configuration at all (not just a misconfigured one), the check should still report a failure for that server. Checks should handle the case where there are no subscriptions or no servers gracefully by returning no results.
