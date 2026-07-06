Implement the Ray-based metrics wrappers in `vllm/v1/metrics/ray_wrappers.py` to correctly handle labeled and unlabeled metrics. Ensure that each labeled metric maintains independent state and that unlabeled metrics include necessary tags.

Requirements:

* Define and export the following classes in `vllm/v1/metrics/ray_wrappers.py`:
    * `RayCounterWrapper`
    * `RayGaugeWrapper`
    * `RayHistogramWrapper`

* Implement the `labels()` method for each wrapper:
    * Return a new independent child object with each call.
    * Populate the child's `_tags` attribute with a dictionary mapping label names to string values.
    * Ensure `_tags` is an independent copy for each child.
    * Coerce non-string label values to strings.
    * Raise `ValueError` with "already-labeled" if `labels()` is called on an already-labeled child.
    * Raise `ValueError` with "Number of labels must match" if the number of label values does not match the number of label names.

* Implement the following methods for each wrapper's child objects:
    * `RayCounterWrapper`:
        * `inc(value=1.0)`: Call the underlying metric's `inc()` with `value` and `_tags`. No-op if `value` is 0.
    * `RayGaugeWrapper`:
        * `set(value)`: Call the underlying metric's `set()` with `value` and `_tags`.
    * `RayHistogramWrapper`:
        * `observe(value)`: Call the underlying metric's `observe()` with `value` and `_tags`.

* Handle unlabeled metrics:
    * For `RayCounterWrapper` with `labelnames=None`, ensure `inc()` passes `tags={'ReplicaId': ''}` to the underlying metric.

* Ensure each wrapper instance has a `metric` attribute:
    * This attribute should be readable and writable.
    * The underlying metric object must have a `_tag_keys` attribute listing declared tag keys.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.