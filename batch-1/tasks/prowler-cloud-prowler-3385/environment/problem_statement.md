## Description

We need to add security checks for Azure MySQL Flexible Server instances to our cloud security platform. Currently, there are no automated checks to verify that MySQL Flexible Servers are properly secured with respect to connection encryption, encryption protocol versions, and audit logging. This creates a gap in our security coverage for Azure database services.

## Expected Behavior

The following new security checks should be implemented:

- A check that verifies whether audit logging is globally enabled on each MySQL Flexible Server.
- A check that verifies whether connection events are included in the audit log events being captured for each server.
- A check that verifies whether encrypted connections are required for each server (i.e., whether unencrypted connections are rejected).
- A check that verifies whether only secure, modern encryption protocol versions are permitted for each server, flagging any servers that allow outdated protocol versions.

Each check should:
- Return a PASS result for servers where the relevant security control is properly configured, and a FAIL result for servers where it is missing or misconfigured.
- Include descriptive messages identifying the server name and subscription in each result.
- Return no results if there are no subscriptions or no servers within a subscription.

Additionally, the underlying service layer should be implemented to retrieve MySQL Flexible Server instances and their configurations from Azure, providing the data these checks rely on.

## Why This Matters

Without these checks, security teams have no automated way to detect Azure MySQL databases that allow unencrypted connections, use outdated encryption protocols, or have audit logging disabled. These misconfigurations increase the risk of data exposure and make it harder to detect unauthorized access or suspicious activity.
