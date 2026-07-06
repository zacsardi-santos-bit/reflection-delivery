## Description

Prowler currently has no support for auditing AWS Lightsail resources. Teams using Lightsail — which provides simplified virtual machines, managed relational databases, and static IP addresses — have no way to include these resources in their Prowler security audits. This means common misconfigurations can go undetected.

## Expected Behavior

Prowler should support scanning Lightsail resources and include the following security checks:

- **Database public access**: Detect when a Lightsail managed database is configured to be publicly accessible from the internet, flagging it as a security risk and passing when access is restricted.
- **Instance automated snapshots**: Detect when a Lightsail virtual machine instance does not have automated backup snapshots enabled, since the absence of backups increases the risk of data loss.
- **Instance public exposure**: Detect when a Lightsail instance has a public IP address and network ports open to the public, reporting the list of exposed port numbers. Instances with no public-facing ports or no public IP should pass.
- **Unused static IPs**: Detect when a Lightsail static IP address is allocated but not attached to any instance, which wastes resources and may represent a security risk. When a static IP is attached to an instance, include the instance name in the passing result.

All checks should report the resource ARN, resource identifier, tags (where applicable), and region.

## Why This Matters

Without Lightsail support, security teams have a blind spot for an entire category of AWS resources. Adding these checks allows organizations to maintain consistent security posture across all their AWS workloads, including those running on Lightsail.
