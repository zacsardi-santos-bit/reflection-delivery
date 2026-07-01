## Description

Prometheus's Linode service discovery currently returns all Linode instances across all geographic regions when refreshing targets. For users who manage instances spread across multiple regions but only need to monitor a specific one, there is no way to filter by region in the service discovery configuration. The only workaround is post-discovery relabeling on the region metadata label, which still requires fetching and processing all instances.

Additionally, IPv6 delegated prefix ranges — entire CIDR blocks routed to individual Linode instances — are not exposed as metadata labels on discovered targets. Linode supports assigning IPv6 ranges to instances (separate from the instance's primary SLAAC address), and users who rely on IPv6 addressing cannot reference these ranges in relabeling rules today.

## Expected Behavior

- A region configuration option should be available in the Linode service discovery settings. When specified, only instances in that region should be returned as scrape targets. When omitted, all regions continue to be discovered (backwards-compatible default behavior).
- Each discovered target should include a new metadata label listing all IPv6 prefix ranges routed to that instance. The label should follow the same comma-separated format as other list-valued labels (e.g., extra IPs and tags). Instances without any routed IPv6 ranges should not have this label.

## Why This Matters

Teams running Linode infrastructure across multiple regions often want separate Prometheus jobs or scrape configurations per region. Without server-side region filtering, every refresh fetches the entire instance list across all regions, increasing API usage and complicating target management. IPv6 range metadata fills a gap for users leveraging Linode's IPv6 prefix delegation feature in their network topology and relabeling strategies.
