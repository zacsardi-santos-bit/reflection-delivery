## Description

The AWS Load Balancer Controller currently supports enabling WAF (both Classic and v2) and Shield Advanced protection on load balancers via ingress annotations. However, there are two issues with the current implementation:

1. **No way to explicitly disable add-ons**: Once an add-on is enabled via annotation, users cannot explicitly opt out of it using an annotation — removing the annotation leaves the protection unchanged rather than disabling it. Users should be able to set a special sentinel value (e.g., "none") to explicitly signal that a particular security add-on should be removed from the load balancer.

2. **Poor error messages during reconciliation**: When the controller fails to get, create, or delete a security add-on association on a load balancer, the error messages don't clearly identify which security service (WAF Classic, WAFv2, or Shield) encountered the problem, making troubleshooting difficult.

## Expected Behavior

- Setting the WAFv2 annotation to a sentinel value of "none" should result in the WAFv2 ACL being disassociated from the load balancer.
- Setting the WAF Classic annotation to "none" should disassociate the Classic ACL.
- Setting the Shield annotation to "false" should disable Shield Advanced protection (the controller should track enabled/disabled state explicitly).
- Error messages when reconciliation fails should clearly name the affected security service (WAFv2, WAF Classic, or Shield).
- Shield protection should only be removed if it was originally created by the controller itself — protections created outside the controller should be left alone.
- Multiple ingresses in a group must agree on the same add-on settings; conflicting values should produce a descriptive error.

## Why This Matters

Operators need a way to fully manage the lifecycle of security add-ons through annotations, including disabling them. Without explicit disable support and without clear error messages, managing these security features in production is error-prone and difficult to debug.
