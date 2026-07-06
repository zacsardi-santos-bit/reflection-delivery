## Description

AWS Security Lake supports custom log sources that allow users to ingest data from their own applications or third-party tools alongside AWS-native log sources. However, the Terraform AWS provider currently has no resource for managing these custom log sources. Users who want to adopt infrastructure-as-code for their Security Lake setup are forced to manage custom sources manually through the AWS console or CLI, creating inconsistency between declared state and actual infrastructure.

## Expected Behavior

- A new Terraform resource should be available for declaring and managing custom Security Lake log sources.
- The resource should allow specifying the source name, source version, event classifications to ingest, an IAM role for data crawling, and a cross-account identity configuration.
- The resource should support the full lifecycle: create, read, and delete.
- Terraform import should work for existing custom log sources, with certain computed attributes excluded from import state verification.
- If the resource is deleted outside Terraform, the provider should detect the drift and correctly handle it.

## Why This Matters

Teams managing AWS Security Lake through Terraform can already declare AWS-native log sources and the data lake itself as code. Without support for custom log sources, part of the Security Lake configuration must be managed separately, breaking the consistency that infrastructure-as-code provides. Adding this resource allows teams to manage their entire Security Lake configuration — including custom sources — in a single, version-controlled Terraform configuration.
