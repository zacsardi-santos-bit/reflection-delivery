## Description

The eBPF agent controller package fails to compile because a helper function used to discover pods on a specific Kubernetes node was renamed as part of ongoing refactoring, but the implementation has not been updated to match. All callers now use the new, more descriptive name, but the function itself still exists under the old name, causing a build failure that prevents every controller test in the package from running.

## Expected Behavior

- A function for retrieving pods on a given node (filtered by a container selector) should be available under its updated name throughout the package.
- The function should accept a Kubernetes clientset, a container selector (including namespace and pod label criteria), and a node name, and should return the matching pod list.
- Once the function is available under the correct name, all agent controller tests — including those for kprobe, TC, XDP, tracepoint, uprobe, and discovered programs — should compile and pass.

## Why This Matters

Because a single undefined symbol causes the entire package to fail to build, none of the agent's controllers can be tested. Fixing the naming inconsistency restores the ability to build and test all controllers in the package and keeps the codebase consistent with the refactoring work already applied elsewhere.
