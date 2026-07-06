## Description

The Kafka topic resource has two configuration fields related to local storage retention for tiered-storage topics that have been defined in the schema for some time, but are completely non-functional. Values that operators set for these fields are silently ignored — they are never forwarded to the Kafka service — and the values returned by the API for these settings are never read back into Terraform state. This means it is currently impossible to manage local retention configuration for tiered-storage Kafka topics through this provider.

## Expected Behavior

- When a user sets the local storage retention byte limit in their Terraform configuration, that value should be sent to the Kafka API and applied to the topic.
- When Terraform reads topic state from the API, both local retention fields should be populated correctly in the state.
- If a user sets the local retention byte limit without also setting the overall retention byte limit, the provider should reject the configuration with a clear error indicating the dependency.
- If a user sets the local retention byte limit to a value larger than the overall retention byte limit, the provider should reject the configuration with a clear error about the invalid relationship between the two values.

## Why This Matters

Operators using Kafka with tiered (remote) storage need to control how much data is kept locally versus offloaded to remote storage. Without these fields working, the only way to configure local retention is through the Kafka API directly, bypassing Terraform entirely and causing drift between managed and actual state. Adding validation for the dependency between local and overall retention prevents silent misconfiguration that could lead to unexpected data loss.
