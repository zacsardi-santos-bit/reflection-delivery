Implement a utility function to identify S3 Express directory buckets based on their naming convention. Develop a generic key-value cache and a singleflight package to deduplicate concurrent operations with type safety.

*   Implement the `IsS3ExpressBucket` function in `pkg/s3utils/utils.go`.
    *   Return `true` if the bucket name ends with `--x-s3` and contains a valid availability zone segment in the format `<region-code>-az<digit>` before the suffix.
    *   Return `false` if the bucket name:
        *   Does not end with `--x-s3`.
        *   Ends with `--x-s3` but lacks a valid AZ segment.
        *   Is an IP address, starts with a dot, or contains consecutive dots or hyphens.
        *   Has an invalid AZ segment format.

*   Develop the `Cache` struct in `pkg/kvcache/cache.go`.
    *   Ensure the zero value of `Cache[K, V]` is usable without initialization.
    *   Implement methods:
        *   `Set(key K, val V)` to store a key-value pair.
        *   `Get(key K) (V, bool)` to retrieve a value and indicate if it was found.
        *   `Delete(key K)` to remove a key.

*   Create the `Group` struct in `pkg/singleflight/singleflight.go`.
    *   Ensure the zero value of `Group[K, V]` is usable without initialization.
    *   Implement methods:
        *   `Do(key K, fn func() (V, error)) (V, error, bool)` to execute `fn` or wait for an in-flight call with the same key. Return a boolean indicating if the result was shared.
        *   `DoChan(key K, fn func() (V, error)) <-chan Result[V]` to return a channel receiving a single `Result`.
        *   `Forget(key K)` to remove the in-flight entry for a key.

*   Ensure `Group.Do`:
    *   Propagates panics from `fn` to all waiting callers.
    *   Handles `runtime.Goexit()` by returning without error.
  
*   Ensure `Group.DoChan`:
    *   Propagates panics from `fn` to crash the process.
    *   Propagates panics from concurrent `Do` calls via `DoChan`.

*   Define the `Result[V]` struct in `pkg/singleflight/singleflight.go`.
    *   Include fields: `Val` of type `V`, `Err` of type `error`, and `Shared` of type `bool`.

*   Implement the `panicError` struct in `pkg/singleflight/singleflight.go`.
    *   Include a field `value` of type `any`.
    *   Implement the error interface.
    *   Ensure `Unwrap()` returns `nil` when `value` is not an error.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.