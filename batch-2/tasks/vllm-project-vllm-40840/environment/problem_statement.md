## Description

The Ray-based metrics wrappers used in vLLM's distributed inference do not correctly support labeled metrics. When a metric is recorded under multiple different label values (for example, tracking request counts broken down by finish reason), the system currently shares label state across all labeled instances rather than maintaining independent per-label tracking. This causes metric values for one label (e.g., "stop") to bleed into or overwrite those for another label (e.g., "length"), making the resulting observability data incorrect.

There is also a related issue with unlabeled metrics: when a metric operation is performed on a wrapper that was created without any label names, the required identifier tag is missing from the call to the underlying Ray metric. Ray requires that every declared tag key be present on every metric update, so omitting it silently corrupts or drops the measurement.

## Expected Behavior

- Calling the labeling method on a metric wrapper with different label values must return separate, independent child objects — each carrying its own distinct tag set.
- Modifying the tag state of one labeled child must not affect any other child.
- Non-string label values must be automatically converted to strings.
- Calling the labeling method more than once on an already-labeled child must be rejected with a clear error.
- Unlabeled metric wrappers must still include the required identifier tag when recording values.

## Why This Matters

vLLM runs multiple engine replicas and tracks rich per-label metrics (finish reasons, request types, etc.) via Ray's metrics system. If labels are shared instead of isolated, dashboards and alerts will show incorrect aggregated or overwritten values, undermining the reliability of the observability pipeline.
