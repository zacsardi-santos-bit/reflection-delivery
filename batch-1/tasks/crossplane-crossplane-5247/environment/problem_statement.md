## Description

Crossplane composition functions often need to access Kubernetes resources that already exist in the cluster but are not part of the composite resource being reconciled. For example, a function might need to read a shared configuration object, a secret, or any other cluster resource to generate the correct desired state. Currently there is no mechanism for a function to declare these dependencies, and there is no way for the composition engine to look them up and supply them.

## Expected Behavior

- Functions should be able to declare, in their response, a set of external resources they require, selecting each group by resource type and either by name or by a set of labels.
- The composition engine should look up those resources from the cluster and pass them back to the function in the next invocation.
- This request-fetch-retry cycle should repeat until the function's declared requirements stop changing between calls.
- The number of iterations should be capped at a fixed maximum to prevent infinite loops.
- The offline rendering tool used for local development and testing should support the same mechanism, accepting a file containing the extra resources that would be present in a real cluster, so that developers can test their compositions without a running cluster.

## Why This Matters

Without this capability, composition functions cannot safely depend on external cluster state. Functions are forced to encode assumptions about that state directly into their logic, making compositions harder to write, test, and maintain. By adding a standard way for functions to declare and receive the resources they need, this feature makes compositions more flexible and enables realistic local testing.
