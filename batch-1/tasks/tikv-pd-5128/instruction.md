Implement a per-service GC safe point management system to allow multiple services to register and update their own safe points independently. Ensure that the system tracks which service holds the current overall minimum safe point and enforces specific constraints for the core GC worker service.

*   Export the `NewSafePointManager` function in the `server/gc` package.
    *   Signature: `NewSafePointManager(storage endpoint.GCSafePointStorage) *SafePointManager`
    *   This function should replace the previously unexported constructor, allowing the manager to be instantiated from outside the package.

*   Implement the `UpdateServiceGCSafePoint` method in the `SafePointManager`.
    *   Signature: `(m *SafePointManager) UpdateServiceGCSafePoint(serviceID string, safePoint uint64, ttl int64, now time.Time) (min *endpoint.ServiceSafePoint, updated bool, err error)`
    *   Automatically initialize the `gc_worker` service with `SafePoint=0` when any service updates its safe point for the first time.
    *   Return `updated=true` and `err=nil` if the proposed `safePoint` is valid and the update succeeds.
    *   Return `updated=false` and a non-nil error if the `serviceID` is 'gc_worker' and the `ttl` is not `math.MaxInt64`.
    *   Return `updated=false` and `err=nil` if the `ttl` is negative, treating it as a service removal request.
    *   Return `updated=false` and `err=nil` if the proposed `safePoint` is less than the current minimum safe point across all registered services.
    *   Return `updated=true` and `err=nil` if the proposed `safePoint` is greater than or equal to the current overall minimum safe point.
    *   Ensure the `min` value returned represents the service with the lowest safe point value across all registered services at the time of the call.

*   Define the `ServiceSafePoint` struct in `server/storage/endpoint/gc_safe_point.go`.
    *   Fields:
        *   `ServiceID string` - Identifier of the service.
        *   `SafePoint uint64` - The safe point value registered by the service.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.