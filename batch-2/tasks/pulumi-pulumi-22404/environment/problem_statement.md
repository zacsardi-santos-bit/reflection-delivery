## Bug: Exclude and target filtering are broken for program-based resource refresh

### Description

When running a program-based refresh operation with resource filtering flags (exclude or target), the filtering logic is inverted. Specifically, resources that are supposed to be excluded from the refresh are actually being refreshed, while the resources that should be refreshed are being skipped. This is the exact opposite of the intended behavior.

Similarly, when using targeted refresh (specifying which resources should be refreshed), the filtering may not behave as expected — only the targeted resources should be refreshed, and all others should remain untouched.

### Expected Behavior

- When a resource is excluded from a refresh operation, it should be left completely unchanged after the refresh. Its state should remain as it was before.
- Resources that are **not** excluded should be refreshed normally, receiving any updated state from the provider.
- When resources are targeted for a refresh, only those specific resources should be refreshed. All other resources should retain their pre-refresh state.

### Why This Matters

Users rely on the exclude and target flags to selectively refresh parts of their infrastructure without accidentally touching resources that should remain stable. When this logic is inverted, users get exactly the wrong behavior — the resources they intended to protect are refreshed, and the resources they wanted to update are skipped. This is a significant correctness bug that can lead to unexpected state changes.
