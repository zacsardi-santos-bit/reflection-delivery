## Description

There is currently no integration in the XSOAR platform for the European Union Vulnerability Database (EUVD), a vulnerability database maintained by the EU cybersecurity agency (ENISA). Security teams have no way to query this database directly from their incident response workflows or automated playbooks.

## Expected Behavior

A new integration should be added that allows analysts to:

- Look up a specific advisory by its ID
- Look up a vulnerability by its ENISA-assigned database identifier
- Look up a vulnerability by its standard CVE/vulnerability ID
- Retrieve the latest critical vulnerabilities from the database
- Retrieve the latest exploited vulnerabilities from the database
- Retrieve the latest vulnerabilities from the database
- Search and filter vulnerabilities using criteria such as base score range, EPSS score range, date range, vendor, product, assigner, exploitation status, keyword text, and pagination controls

Commands that require an identifier (advisory ID, ENISA ID, or vulnerability ID) should return an informative error when that identifier is not provided.

The integration's connectivity test should verify that the API is reachable and return a success indicator when it is.

## Why This Matters

Many organizations need to cross-reference vulnerability data from the EU agency's database as part of their triage and response process. Without a native integration, analysts must leave the platform to consult the database manually, slowing down response times and making automation impossible.
