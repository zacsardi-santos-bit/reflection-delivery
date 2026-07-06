Update the device listing service and data layer to correctly determine device acceptability across different deployment modes (cloud, enterprise, community). Ensure that the logic for marking devices as "acceptable" is correctly applied based on the namespace's capacity and deployment mode.

*   Update the `ListDevices` function in `api/services/device.go`:
    *   Always call `NamespaceGet(ctx, tenant)` first; return `NewErrNamespaceNotFound` if it fails.
    *   If `status == DeviceStatusRemoved`, call `DeviceRemovedList(ctx, tenant, paginator, filters, sorter)`, convert results to `[]models.Device`, and return.
    *   For namespaces with a max device limit (`HasMaxDevices()`):
        *   In cloud mode (`envs.IsCloud()`):
            *   Call `DeviceRemovedCount`; if it fails, return `NewErrDeviceRemovedCount`.
            *   If `namespace.HasLimitDevicesReached(removedCount)`, call `DeviceList` with `DeviceAcceptableFromRemoved`.
        *   In community or enterprise mode (`envs.IsCommunity()` or `envs.IsEnterprise()`):
            *   If `namespace.HasMaxDevicesReached()`, call `DeviceList` with `DeviceAcceptableAsFalse`.
    *   Otherwise (under limit or no limit), call `DeviceList` with `DeviceAcceptableIfNotAccepted`.
    *   Ensure community mode does not call `DeviceRemovedCount`.

*   Modify the store package:
    *   Define three constants in `api/store/device.go`:
        *   `DeviceAcceptableAsFalse` to replace `DeviceListModeMaxDeviceReached`.
        *   `DeviceAcceptableIfNotAccepted` to replace `DeviceListModeDefault`.
        *   `DeviceAcceptableFromRemoved` for cloud mode when the namespace has reached its device limit.
    *   Update `DeviceList` function in `api/store/mongo/device.go` and `api/store/device.go` to use `store.DeviceAcceptable`:
        *   Handle `DeviceAcceptableIfNotAccepted` to set `Acceptable=true` for non-accepted devices.
        *   Handle `DeviceAcceptableAsFalse` to set `Acceptable=false` for all devices.
        *   Handle `DeviceAcceptableFromRemoved` to perform a lookup against the `removed_devices` collection.

*   Implement `NewErrDeviceRemovedCount` function in `api/services/`:
    *   Accept an error and return an error representing a failure to count removed devices.

*   Update the mock at `api/store/mocks/store.go` to use `store.DeviceAcceptable` in the `DeviceList` mock function signature and type assertions.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.