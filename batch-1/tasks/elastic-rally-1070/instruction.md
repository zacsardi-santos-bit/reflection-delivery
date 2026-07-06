Implement support for passing custom request timeouts, custom HTTP headers, and opaque request identifiers to Elasticsearch API calls in Rally's benchmark operations. Update the relevant runner classes to handle these parameters and ensure they are correctly forwarded or omitted as specified.

*   Update the `BulkIndex` class in `esrally/driver/runner.py`:
    *   Accept `request-timeout`, `headers`, and `opaque-id` in the `params` dict.
    *   Forward them to the Elasticsearch bulk API as `request_timeout`, `headers`, and `opaque_id` keyword arguments.

*   Update the `ForceMerge` class in `esrally/driver/runner.py`:
    *   For the modern path, forward `request-timeout`, `headers`, and `opaque-id` to `es.indices.forcemerge()` as keyword arguments.
    *   For the legacy optimize path, call `es.transport.perform_request()` with `method='POST'`, `url='/_optimize'`, and include `request_timeout` and `max_num_segments` in `params` if provided. Merge `opaque-id` into `headers` as `x-opaque-id` if present.

*   Update the `IndicesStats` class in `esrally/driver/runner.py`:
    *   Accept `request-timeout`, `headers`, and `opaque-id` in the `params` dict.
    *   Forward them to `es.indices.stats()` as `request_timeout`, `headers`, and `opaque_id` keyword arguments.

*   Update the `Query` class in `esrally/driver/runner.py`:
    *   Accept `request-timeout`, `headers`, and `opaque-id` in the `params` dict.
    *   Include `request-timeout` in the `params` dict as `request_timeout` and merge `opaque-id` into `headers` as `x-opaque-id` for `es.transport.perform_request()`.

*   Update the `ClusterHealth` class in `esrally/driver/runner.py`:
    *   Accept `request-timeout`, `headers`, and `opaque-id` in the `params` dict.
    *   Forward them to `es.cluster.health()` as `request_timeout`, `headers`, and `opaque_id` keyword arguments. Do not pass `index=None` if `index` is not specified.

*   Update the `CreateIndex` class in `esrally/driver/runner.py`:
    *   Accept `request-timeout`, `headers`, and `opaque-id` in the `params` dict.
    *   Forward them to `es.indices.create()` as `request_timeout`, `headers`, and `opaque_id` keyword arguments. Exclude top-level `index` and `body` from the outer params dict.

*   Update the `RawRequest` class in `esrally/driver/runner.py`:
    *   Accept `request-timeout`, `headers`, and `opaque-id` in the `params` dict.
    *   Include `request-timeout` in the `params` kwarg dict as `request_timeout` and merge `opaque-id` into `headers` as `x-opaque-id` for `es.transport.perform_request()`.

*   Update the `RestoreSnapshot` class in `esrally/driver/runner.py`:
    *   Ensure `body=None` is not passed to `es.snapshot.restore()` when no body is specified.

*   Update the `SearchParamSource` class in `esrally/track/params.py`:
    *   Ensure `params()` returns a dict with 9 keys, including `request-timeout`, `headers`, and `opaque-id`, defaulting to None if not configured.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.