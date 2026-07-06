## Description

Prefixes and VLANs in NetBox already support being assigned a functional role to categorize them by purpose, but Autonomous System Numbers (ASNs) have no equivalent capability. This makes it difficult to organize and manage large ASN inventories where different ASNs serve different operational functions (e.g., transit, peering, internal).

## Expected Behavior

- Users should be able to assign an optional role to an ASN when creating or editing it through the web UI
- The ASN list and detail pages should support filtering by role
- The REST API for ASNs should accept and return a role field
- Bulk editing of ASNs should allow setting or clearing the role across multiple records at once
- CSV import of ASNs should accept an optional role column (matched by role name)
- The role overview page should display an ASN count for each role, alongside the existing prefix and VLAN counts
- The brief/compact API representation of a role should include an ASN count field

## Why This Matters

Without role support on ASNs, network operators cannot use NetBox's existing role taxonomy to classify ASNs by function, which is inconsistent with how prefixes and VLANs work. Adding this capability brings ASN management in line with the rest of the IP address management workflow.
