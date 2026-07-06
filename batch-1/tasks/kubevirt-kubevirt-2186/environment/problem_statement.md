## Description

KubeVirt's live migration validation does not account for data volumes when checking whether a virtual machine instance can be safely migrated. Currently, only traditional persistent volume claims are inspected during migration eligibility checks — if a VMI uses a data volume as its disk source, that data volume is ignored entirely during validation, meaning VMs backed by non-shared data volumes can incorrectly appear as migratable and only fail later.

Additionally, there is currently no mechanism to override migration safety restrictions for specific use cases. When a VM's disk is backed by a shared filesystem-type volume (such as an iSCSI volume with an ext4 filesystem), the underlying hypervisor layer will refuse to migrate it for safety reasons, and there is no way for an administrator to explicitly allow such migrations when they know the environment is safe.

## Expected Behavior

- When a VMI has a data volume backed by non-shared storage, migration attempts should be rejected immediately with a clear error indicating the disks are not live-migratable.
- The VMI's migratability status condition should reflect that it cannot be migrated when it has non-shared data volume storage.
- A cluster-wide configuration option should be available that, when enabled, permits migrations of VMs with shared filesystem-type volumes by disabling the hypervisor's normal migration safety checks.
- Existing helper utilities for setting up iSCSI-backed test storage should support both filesystem and block volume modes, rather than being limited to block mode only.

## Why This Matters

Without these changes, data volumes are a blind spot in migration validation — non-migratable VMs may appear migratable and fail in unexpected ways. The unsafe migration override is needed to support legitimate use cases, like migrating VMs with shared iSCSI ext4 filesystem volumes, which would otherwise be unconditionally blocked even when the storage configuration is safe.
