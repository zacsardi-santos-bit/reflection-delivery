## Description

The security scanning tool currently checks for privilege escalation risks and overly-permissive access to sensitive services in managed (customer-created) IAM policies, but it lacks equivalent coverage for **inline policies** — those embedded directly in IAM roles, users, and groups. This gap means that a role or user with a dangerous inline policy could go undetected even while managed policies are flagged correctly.

In addition, the existing checks for detecting full access to certain critical services only look for explicit wildcard action entries. They miss cases where a policy uses an "allow everything except" construct that implicitly grants full access to any service not listed in the exclusion list — which is an equally dangerous misconfiguration.

Finally, the privilege escalation combination logic is currently duplicated between the implementation and the tests, making it harder to keep them in sync.

## Expected Behavior

- Inline policies attached to roles, users, or groups should be scanned for privilege escalation risks, producing one finding per inline policy.
- Inline policies should also be checked for granting unrestricted access to critical services (such as logging and encryption key management).
- Full-service-access detection should correctly flag policies that use "allow all except listed services" statements where the critical service is not in the exclusion list.
- The privilege escalation combination definitions should live in a single shared library module, imported by all relevant checks.

## Why This Matters

Inline policies are commonly used in AWS environments and represent the same security risks as managed policies. Without these checks, security teams have an incomplete picture of their IAM risk surface. This gap also means policies crafted to look restrictive can actually grant wide-open access to critical services, which these checks would previously miss.
