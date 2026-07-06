## Description

The Microsoft Defender for Endpoint integration supports querying software inventories, missing security updates, and vulnerabilities at the organization level, but there are no commands to retrieve this information scoped to a **specific machine**. When responding to an incident, analysts need to quickly determine what software is installed on a particular device, which security patches it is missing, and what vulnerabilities affect it — without having to filter through organization-wide result sets.

## Expected Behavior

- A new command should allow analysts to retrieve the list of software installed on a specific machine, returning details such as software name, vendor, whether a public exploit exists, the number of exposed machines, and whether the software is normalized.
- A new command should allow analysts to retrieve the missing security updates (KBs) for a specific machine, returning details such as the KB ID, name, OS build, URL, and number of CVEs addressed.
- A new command should allow analysts to retrieve the known vulnerabilities affecting a specific machine, returning CVE details including severity, CVSS scores, exploit information, and detection timestamps.

All three commands should accept a machine identifier as input and return structured outputs suitable for use in playbooks.

## Why This Matters

Security analysts investigating a compromised or at-risk device need per-machine context fast. Without these commands, they must query organization-wide lists and manually correlate data, which slows down triage and response. Per-machine queries significantly reduce investigation time for endpoint-focused workflows.
