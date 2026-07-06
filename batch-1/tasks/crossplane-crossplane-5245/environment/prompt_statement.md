I'm working on the Crossplane CLI and I'd like to add a new subcommand under the beta group that shows CPU and memory usage for Crossplane-related pods — similar to what the standard Kubernetes tooling provides, but scoped specifically to Crossplane components. The output should be a formatted table with columns for pod type, namespace, name, CPU (in millicores), and memory (in mebibytes). There should also be an optional summary mode that shows aggregate totals broken down by pod type, plus total CPU and memory across all Crossplane pods.

The command needs to classify pods by inspecting their labels. Pods with labels in the Crossplane package namespace (keyed by component type) should be identified by that type, and the core Crossplane controller pod (labeled as part of Crossplane) should appear as its own type. The pod type names in the summary should be displayed with the first letter capitalized, listed alphabetically.

I also need a small helper that capitalizes only the first character of a string — returning the string unchanged if it's empty or if the first character is not a letter.

Could you help implement this as a new package in the CLI, with the necessary classification, table-printing, and summary-printing logic?
