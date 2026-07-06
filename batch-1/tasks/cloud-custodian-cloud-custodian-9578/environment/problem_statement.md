## Description

Cloud Custodian supports many AWS resource types with rich filtering capabilities, but the MemoryDB resource currently lacks several critical network and encryption filters. Teams cannot write policies that inspect the security groups, subnets, or KMS encryption keys associated with MemoryDB clusters, and the network-location filter — which compares tags between a resource and its associated network resources — is also missing.

## Expected Behavior

- It should be possible to filter MemoryDB clusters based on the attributes of their associated security groups (for example, by security group tags).
- It should be possible to filter MemoryDB clusters based on the attributes of their associated subnets (for example, by subnet name or tag). Because MemoryDB clusters reference subnets via a subnet group, the filter must resolve subnet group membership to identify which subnets a cluster uses.
- It should be possible to apply a network-location comparison filter to MemoryDB clusters, comparing tags or attributes between the cluster and its associated subnets.
- It should be possible to filter MemoryDB clusters by the KMS key used for encryption, including matching on key aliases.

## Why This Matters

Without these filters, security and compliance teams have no way to enforce policies such as "MemoryDB clusters must use security groups tagged for production use", "clusters must reside in subnets in the private network tier", or "all clusters must use a customer-managed encryption key with a specific naming convention". Adding these filter types brings MemoryDB in line with the filtering capabilities available for other database resource types.
