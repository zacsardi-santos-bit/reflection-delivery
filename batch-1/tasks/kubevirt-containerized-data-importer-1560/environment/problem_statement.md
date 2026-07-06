# DataVolume Import Stalls When Storage Class Uses Deferred Volume Binding

## Description

When a Kubernetes storage class is configured to defer volume binding until a consumer actually schedules a workload (a common pattern on many cloud providers), the CDI data importer does not start processing the data volume. The volume never gets bound, so the import, upload, blank image creation, or clone operation stalls indefinitely.

There is currently no way for users to indicate that a specific data volume should have its binding happen immediately, bypassing the deferred-binding behavior of the storage class.

## Expected Behavior

- Users should be able to annotate a DataVolume to explicitly request immediate volume binding, even when the storage class would otherwise defer it.
- When immediate binding is requested via this annotation, the controller should treat the data volume as eligible for reconciliation and proceed with the import/upload/clone operation.
- When the annotation is not set and the deferred-binding feature is enabled, the controller should continue to skip unbound volumes (existing behavior).
- DataVolumes annotated for immediate binding should successfully reach their expected terminal state (completed for imports and clones, ready-for-upload for upload volumes).

## Why This Matters

Without a way to override deferred binding per data volume, users are blocked from using CDI on storage classes that default to this binding mode, even when they specifically want the data volume to be populated right away. This annotation provides the necessary escape hatch for those use cases.
