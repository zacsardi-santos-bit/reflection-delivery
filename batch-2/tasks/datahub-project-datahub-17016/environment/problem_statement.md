## Description

DataHub's metadata ingestion framework currently has no built-in way to retrieve secret values stored in cloud-hosted secret management services. Users who follow security best practices by storing credentials in their cloud provider's secrets service are forced to hardcode sensitive values directly in configuration files instead of referencing them from a secure store.

## Expected Behavior

- The ingestion framework should include a store implementation for AWS Secrets Manager that connects to a specified AWS region, uses a configurable prefix to namespace secret names, and retrieves secrets by their logical names.
- The same capability should exist for Google Cloud Secret Manager, using a project ID and configurable prefix.
- Both stores should expose a consistent interface: a factory method for constructing the store from a configuration dict, a method to retrieve multiple secrets at once by name, and a convenience method to retrieve a single secret.
- When a requested secret does not exist or is inaccessible, the store should return a null value for that key rather than raising an error.
- Each store should have a stable string identifier that uniquely identifies which backend is in use.

## Why This Matters

Without native support for cloud secret stores, teams cannot follow the principle of keeping secrets out of configuration files. Adding these integrations allows ingestion pipelines to securely fetch credentials at runtime from AWS Secrets Manager or GCP Secret Manager, without any secrets appearing in plain text in configuration.
