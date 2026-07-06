## Description

VictoriaMetrics currently supports service discovery for several cloud providers and platforms, but there is no built-in support for Hetzner — a popular hosting provider offering both a modern cloud platform and a legacy dedicated server (robot) platform. Users who run their infrastructure on Hetzner must manually maintain static scrape target configurations, which is error-prone and does not scale as servers are added or removed.

This issue requests adding native Hetzner service discovery so that VictoriaMetrics can automatically discover and monitor Hetzner servers from both the cloud and robot APIs.

## Expected Behavior

- When configured for the Hetzner Cloud role, the scraper discovers cloud servers and attaches metadata labels for server identity, public IPv4 and IPv6 addresses, datacenter location, network zone, server hardware type (including CPU cores, CPU type, memory, and disk size), OS image details, and the private IP address in each configured network.
- When configured for the Hetzner Robot role, the scraper discovers dedicated servers and attaches metadata labels for server identity, public IPv4 and IPv6 network addresses, datacenter, hardware product, and cancellation status.
- Both roles share a common set of base labels (server ID, name, status, public IPs, datacenter), while role-specific labels provide additional platform-specific metadata.
- Parsing the JSON API responses from both Hetzner platforms must succeed without error for valid response bodies.

## Why This Matters

Many teams host production workloads on Hetzner. Without this feature, they cannot take advantage of VictoriaMetrics' dynamic scrape configuration and must maintain manual target lists. Native Hetzner service discovery brings Hetzner users on par with users of other supported cloud providers.
