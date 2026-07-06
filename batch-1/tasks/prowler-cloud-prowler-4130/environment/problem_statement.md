## Description

Prowler currently does not include a check for verifying whether AWS accounts have configured event notifications for RDS database security group events. Specifically, there is no automated way to determine whether the required event categories — configuration change and failure — are being monitored via active subscriptions.

Without this check, security teams have no automated signal from Prowler indicating whether their environments are configured to receive alerts when someone modifies a database security group or when a security group experiences a failure. This is an important security visibility gap.

## Expected Behavior

- When there are no event subscriptions at all, or when existing subscriptions are not configured for database security group events, the check should fail with a message noting that both the configuration change and failure categories are not subscribed.
- When an active database security group event subscription covers all event categories broadly, the check should pass.
- When an active subscription exists but covers only the failure event category, the check should fail indicating the configuration change category is not covered.
- When an active subscription exists but covers only the configuration change category, the check should fail indicating the failure category is not covered.
- When the scan is configured to skip services with no active resources, the check should produce no findings if no RDS instances are present.

## Why This Matters

Monitoring RDS security group events is important for detecting unauthorized changes and failures in database access controls. Without this check, Prowler users cannot automatically audit whether their AWS accounts are set up to receive alerts for these critical events.
