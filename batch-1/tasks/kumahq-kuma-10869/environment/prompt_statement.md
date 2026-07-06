I'm working on adding support for fine-grained reachable backend references in our service mesh proxy configuration. Right now, the system can only express reachability at the level of service tag sets, but we want to support declaring which specific named backend resources a workload can reach — by kind and name, by namespace, or by label selectors.

There are a few things I need to implement:

First, the dataplane configuration validator needs to validate a new list of backend references in the transparent proxy settings. Each entry can reference a backend by name (with optional namespace) or by label selectors, and the allowed kinds are the same three resource types we already support elsewhere. The validator should check that: only valid kinds are used, name and labels aren't both specified at once, namespace requires a name, either name or labels must be present, and names must be valid RFC 1123 subdomain strings.

Second, the reachability graph needs to be refactored to support two separate kinds of reachability checks: the existing service-level check (which uses tag sets to identify destinations) and a new backend-level check (which uses a specific resource kind and name to identify the destination). The graph constructor should accept both sets of precomputed rules. The backend-level check should evaluate traffic permission policies that have been matched against each named backend resource.

Third, there needs to be a new subpackage that handles building the rules for named backends. It processes a list of mesh service resources and traffic permission policies, matching the policies to each service to determine which sources are allowed to reach it. Importantly, the policy matching should filter out tags that don't apply to a given service's context, and the original policy objects must not be modified in the process.

The existing service-based rule-building logic should be moved into its own subpackage as well, so the graph package itself is just responsible for the graph structure and the reachability methods.

Also, the error message for an invalid backend kind in outbound references currently lists only two valid kinds — it needs to be updated to include the third supported resource type.
