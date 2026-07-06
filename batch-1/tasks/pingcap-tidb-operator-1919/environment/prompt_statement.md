I'm working on the Prometheus configuration generator for our Kubernetes operator. We have two issues to fix in the generated scrape configs for our three core database components.

First, the current address relabeling logic extracts the IP address from the pod's address field and combines it with a port annotation. This approach is fragile — we need to switch to a DNS-based address using the pod name and cluster instance label to construct the target via the component's peer service. The resulting address should follow the pattern of using the pod name, the cluster name, the component type, and the port together.

Second, the scheme setting (whether to use plain HTTP or HTTPS) needs to be explicitly included in the generated config and must respond correctly to whether cluster TLS is enabled. When TLS is enabled, two of the three components should be scraped over HTTPS with the cluster client certificate files and strict certificate verification. The third component doesn't support HTTPS scraping yet, so it should stay on HTTP with certificate verification skipped — even when the cluster TLS flag is on. When TLS is not enabled at all, all three components should explicitly use HTTP.

The configuration rendering function needs to handle both the TLS-enabled and non-TLS cases correctly, and the model passed to it should carry the flag indicating whether cluster TLS is active.
