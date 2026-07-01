I'm working on adding Hetzner service discovery support to VictoriaMetrics. Hetzner offers two distinct server platforms — a cloud platform and a dedicated server (robot) platform — and I want VictoriaMetrics to be able to automatically discover servers from both.

For the cloud platform, I need to parse the server list and network list responses from the Hetzner Cloud API, and then generate Prometheus-compatible metadata labels for each discovered server. These labels should cover the server's identity, public IPv4 and IPv6 addresses, datacenter name, datacenter location and network zone, server hardware type details (CPU cores, CPU type, memory size in GB, disk size in GB), OS image information (name, description, OS flavor and version), and private IP addresses keyed by network name.

For the dedicated server platform, I need to parse the robot server list response (which is a JSON array) and generate metadata labels covering the server's identity, public IPv4 address, public IPv6 network (taken from the subnet information), datacenter name (lowercased), hardware product name, and cancellation status.

Both sets of parsing logic need to correctly handle the JSON field naming conventions used by the respective APIs — including cases where JSON field names differ from what you might expect from the Go struct field names. The resulting label sets must be complete and accurate so they can be used for target relabeling in scrape configurations.

The implementation should be organized into two separate files within the Hetzner discovery package — one for the cloud platform and one for the robot platform.
