## Description

When the Airflow integration plugin captures lineage for datasets used in tasks (as inputs or outputs), it currently emits a generic existence signal for each dataset — essentially recording that the dataset is present and not deleted. This is a low-value side effect that doesn't add meaningful information to the data catalog.

A better approach would be to emit the dataset's structural identity information — its platform, name, and environment — which is already fully known from the dataset's identifier. This eliminates the superfluous existence flag in favor of more informative key metadata that downstream consumers can actually use.

## Expected Behavior

- When the plugin processes a dataset as a task inlet or outlet, it should emit structural identity metadata (key information) for that dataset, not an existence flag.
- The identity metadata should include the dataset's data platform, its name, and its environment/origin.
- This should work consistently whether or not task execution capture is enabled.
- The behavior should apply for all supported platforms (e.g., different cloud data warehouses and local databases).

## Why This Matters

This change makes the metadata emitted for datasets richer and more useful out-of-the-box, without requiring any additional network lookups or configuration changes. Consumers of the metadata catalog receive structured identity information about each dataset referenced in Airflow tasks, rather than just a flag indicating the entity is not deleted.
