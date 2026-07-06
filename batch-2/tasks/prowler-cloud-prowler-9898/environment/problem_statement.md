## Description

Two improvements are needed in the cloud security check suite targeting Azure and Microsoft 365 Entra configurations.

**1. Rename and fix an existing Azure check**

An existing check that verifies whether a conditional access policy enforces multi-factor authentication for management API access is currently named in a way that doesn't clearly reflect its scope. It should be renamed to better convey that it is specifically a conditional access policy check. Additionally, when the check produces a finding (either passing or failing), it currently reports the tenant domain and tenant ID as the resource — this is misleading because the check is really evaluating the conditional access policy, not the tenant itself. The reported resource name and resource ID should both consistently reference the conditional access policy.

**2. Add a new Microsoft 365 check for the default application management policy**

There is currently no check that verifies whether the default application management policy in a Microsoft 365 tenant is both enabled and properly configured. Administrators need to be alerted when this policy is missing, disabled, or lacks specific required credential restrictions. The required restrictions cover four areas: blocking new password additions, enforcing maximum password lifetimes, blocking custom passwords, and limiting certificate lifetimes. Each missing or inadequately configured restriction should be identified separately in the finding details so administrators know exactly what needs to be fixed.

## Expected Behavior

- The renamed Azure check produces findings that reference the conditional access policy as the resource (not the tenant).
- The new M365 check returns no findings if the policy does not exist.
- The new M365 check fails if the policy is not enabled.
- The new M365 check fails and lists each specific missing or disabled restriction if any of the four required restrictions are absent or not in an enabled state.
- The new M365 check passes if the policy is enabled and all four required restrictions are configured and enabled.
- When the policy has no identifier, the tenant domain is used as a fallback resource identifier.

## Why This Matters

Inconsistent resource identification in findings makes triage harder for security teams — renaming the check and fixing the resource fields improves clarity. The new M365 check closes a gap where weak application credential policies could go undetected, potentially allowing attackers to add long-lived or custom credentials to registered applications.
