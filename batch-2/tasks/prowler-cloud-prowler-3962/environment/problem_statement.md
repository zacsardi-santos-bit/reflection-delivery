## Description

The security scanner currently has a check that is supposed to detect when a cloud firewall rule exposes any port to the internet. However, this check's description is misleading: its status messages say "all ports open" when it should say "at least one port open." Additionally, the check's logic was only catching the most extreme case (all ports exposed) rather than any exposed port — meaning a rule that opens just one specific port to the internet was incorrectly reported as compliant.

There is also a missing check for the distinct, more critical scenario where a firewall rule exposes *all* ports to the internet (e.g. via a wildcard protocol rule). This is a different and more severe finding than any individual port being exposed, and security teams need to be able to flag it separately.

## Expected Behavior

- A new check should be added that specifically detects when a security group allows all ports to be accessed from the internet. It should only trigger for rules that cover all traffic (not just a single specific port). A security group with only port 80 open to the internet should pass this check.
- The existing any-port check should be updated so that a security group with even one specific port open to the internet is flagged as non-compliant, with corrected messaging that reflects "at least one port" rather than "all ports."
- Both checks should respect the "ignore unused services" setting — when a VPC has no resources attached, its security groups should be skipped.

## Why This Matters

Conflating "all ports open" with "any port open" causes false negatives (individual exposed ports go undetected) and misleading findings. Separating these into two distinct checks allows security teams to triage the most critical misconfigurations first while still surfacing all internet-exposed ports.
