I'm working on adding AWS Lightsail support to Prowler. Right now, Prowler doesn't scan any Lightsail resources at all — there's no service integration and no security checks. I need to implement a full Lightsail service module that discovers instances, managed databases, and static IP addresses, along with four security checks on top of it.

The service should collect all three resource types from the relevant AWS APIs and model them with appropriate data structures. For instances, I need to track things like networking ports (including whether each port is public or private), whether automated backup snapshots are enabled (based on the instance's add-ons configuration), and the instance's IP addresses. For databases, I need to know whether public access is enabled. For static IPs, I need to know whether they are currently attached to an instance and if so, which one.

The four checks I need are:
1. Flag any database that is configured to allow public internet access.
2. Flag any instance that does not have automated snapshots enabled.
3. Flag any instance that has a public IP address and has at least one network port with public access — and report which ports are open.
4. Flag any static IP address that is not attached to any instance.

Each check should produce a clear, human-readable status message for both passing and failing cases, following the conventions used by other Prowler checks (resource ARN, resource ID, tags, region). Static IP checks should report an empty tags list since that resource type doesn't carry tags.
