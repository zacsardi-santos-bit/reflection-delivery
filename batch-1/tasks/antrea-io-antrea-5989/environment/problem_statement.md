## Description

The endpoint query feature, which lets users inspect which network policies apply to a pod and which policy rules reference that pod, currently returns all information in a flat, undifferentiated structure. Applied policies, ingress rules, and egress rules are all mixed together, making it hard to understand how a pod participates in network policy traffic decisions.

Additionally, the response types for endpoint queries are currently defined inside the network policy controller package rather than in the API handler package where they logically belong. This makes the architecture awkward because the HTTP handler has to depend on internal controller types for its wire format.

Similarly, the CLI output function that formats endpoint query results is embedded as a method on an internal type rather than being a standalone, reusable package-level function.

## Expected Behavior

- The endpoint querier should return a richer response that clearly separates: (1) policies directly applied to the queried pod, (2) ingress rules that reference the pod as a source, and (3) egress rules that reference the pod as a destination.
- Response types for the HTTP endpoint handler should be defined in the handler package itself.
- The CLI output formatter for endpoint query results should be a package-level function in the output package and use descriptive section labels: "Applied Policies on Endpoint", "Egress Rules Referencing Endpoint as Destination", and "Ingress Rules Referencing Endpoint as Source".
- When no policies or rules exist for a section, the output should clearly indicate "None" for that section.
- When no pod is found matching the query, the endpoint returns a not-found response rather than an empty result.

## Why This Matters

Users querying endpoint policy information need to quickly understand not just which policies select a pod, but also precisely which rules treat the pod as an ingress source or egress destination. The current output format combines these in a way that is ambiguous and hard to read. Improving the structure and labeling makes operational troubleshooting significantly easier.
