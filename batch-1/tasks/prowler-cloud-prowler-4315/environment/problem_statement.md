## Description

The current outputs system uses a legacy data model for representing security findings across multiple output formats. Converting a raw check output into a reportable finding requires several intermediate steps, including a helper function that manually maps provider-specific fields and constructs the legacy model. This logic is scattered and makes it difficult to add new output formats or modify how findings are represented.

We need a new, unified finding data model that:
- Replaces the legacy finding model as the canonical representation of a finding
- Knows how to construct itself directly from a provider and a check output (handling provider-specific naming conventions for authentication method display, resource identifiers, region/namespace labeling, and account metadata)
- Works across all supported cloud providers (AWS, Azure, GCP, Kubernetes)

Additionally, the output format system needs a proper abstract base class that establishes a consistent interface for all output implementations, along with a rewritten CSV output class that correctly transforms finding data (handling lists and dictionaries as formatted strings) and writes semicolon-delimited files.

## Expected Behavior

- A single unified finding class can be constructed directly from a provider + check output via a class method
- AWS findings: auth method shown as "profile: {name}", resource name from resource ID, resource UID from ARN
- Azure findings: auth method shown as "{identity type}: {identity ID}", resource name and ID from the check output
- GCP findings: auth method shown as "Principal: {name}", account info sourced from the project object
- Kubernetes findings: auth method shown as "in-cluster" (for in-cluster context) or "kubeconfig", region shown as "namespace: {name}", account name shown as "context: {name}"
- CSV output correctly serializes tag lists (pipe-separated) and compliance dicts (key-value pairs)
- The legacy helper function for converting findings to the output model is removed
- CSV file descriptor creation is handled by the new CSV output class, not by the general file descriptor initializer

## Why This Matters

Centralizing the provider-specific logic into the finding model itself makes the codebase cleaner and easier to extend. New output formats only need to implement a simple interface rather than duplicating the provider-specific field mapping logic.
