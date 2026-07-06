## Description

The AWS App Mesh integration in Cloud Custodian has two related problems that make it difficult to work with mesh and virtual gateway resources effectively.

**Problem 1: Mesh policies can't filter on spec-level attributes**

When running a policy against App Mesh meshes, the resources returned only contain the basic listing data (mesh name, ARN, owner, etc.). The full specification data — such as egress filter settings or service discovery preferences — is never included in the resource objects that filters operate on. This means any policy trying to filter meshes by their configuration (e.g., "find all meshes where egress is set to a specific restrictive value") silently returns wrong results, because the spec fields are simply absent. The underlying issue is that the resource enrichment (fetching full details via describe) must happen before tags are fetched, and the data extracted from the describe response needs to be properly scoped to the mesh object nested within the API response.

**Problem 2: Reporting output is broken for App Mesh resources**

The report command does not correctly show creation dates or names for App Mesh resources. For mesh resources, no date column appears because the creation date field is not declared in the resource type definition. For virtual gateway resources, the situation is worse: the ARN, name, and creation date are all stored inside a nested metadata sub-object, but the reporting system can only look up top-level fields. This means the CSV output is either empty or incorrect.

## Expected Behavior

- Policies filtering on mesh spec attributes (such as egress filter type) should return only the meshes that match
- The mesh describe call should happen for all resources before tag fetching occurs, in the correct order
- Report output for mesh resources should include the mesh name and creation date
- Report output for virtual gateway resources should include the full ARN (from the nested metadata), the gateway name, and the creation date (also from nested metadata)
- The reporting system must support dot-notation field paths so that nested resource fields can be referenced

## Why This Matters

Without these fixes, Cloud Custodian users cannot reliably audit or govern their App Mesh resources. Policies appear to work but return incorrect results, and the reporting feature produces incomplete output that is difficult to use for compliance or audit purposes.
