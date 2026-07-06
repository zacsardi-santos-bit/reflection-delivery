I'm working on Ray's monitoring system and I'd like to improve the component memory metrics it exposes.

*   The METRICS_GAUGES dictionary in python/ray/dashboard/modules/reporter/reporter_agent.py must contain a new entry keyed 'component_rss_bytes' whose Gauge object has the name 'component_rss_bytes', description 'RSS usage of all components on the node.', and unit 'bytes'.

*   The METRICS_GAUGES dictionary must contain a new entry keyed 'component_uss_bytes' whose Gauge object has the name 'component_uss_bytes', description 'USS usage of all components on the node.', and unit 'bytes'.

*   The METRICS_GAUGES dictionary must rename the existing entry 'component_mem_shared_bytes' to 'component_shared_bytes', with the Gauge name also changed to 'component_shared_bytes'.

*   The ReporterAgent._to_records(stats, cluster_stats) method must produce exactly 49 records when stats include all components and raylets, 46 records when stats['raylet'] is None, 55 records when GPU stats are added, and 53 records when the autoscaler report is absent.

*   When per-component USS records are emitted under the gauge named 'component_uss_bytes', the value must be float(memory_full_info.uss) — the raw byte value with no division by 1.0e6.

*   The Prometheus metrics exported by the reporter must include 'ray_component_rss_bytes' and 'ray_component_uss_bytes' alongside the existing 'ray_component_rss_mb' and 'ray_component_uss_mb' metrics.

*   The Prometheus metrics exported for node components on Linux must include 'ray_component_rss_bytes', 'ray_component_uss_bytes', and 'ray_component_shared_bytes' (the renamed form of the former shared-memory metric).

*   When get_system_metric_for_component is called with the metric name 'ray_component_uss_bytes' and the Prometheus server returns HTTP 500 errors on all retry attempts, the resulting RuntimeError message must contain the string 'ray_component_uss_bytes'.


*   Interface details: Type: Module-level dictionary
Name: METRICS_GAUGES
Location: python/ray/dashboard/modules/reporter/reporter_agent.py
Description: Dictionary mapping gauge name strings to Gauge objects used to construct metric records. Must be updated with the following changes:
  - Add key "component_rss_bytes" → Gauge("component_rss_bytes", "RSS usage of all components on the node.", "bytes", COMPONENT_METRICS_TAG_KEYS)
  - Add key "component_uss_bytes" → Gauge("component_uss_bytes", "USS usage of all components on the node.", "bytes", COMPONENT_METRICS_TAG_KEYS)
  - Rename key "component_mem_shared_bytes" to "component_shared_bytes", and update the Gauge name argument from "component_mem_shared_bytes" to "component_shared_bytes"

Type: Method
Name: _to_records
Location: python/ray/dashboard/modules/reporter/reporter_agent.py (class ReporterAgent)
Signature: _to_records(self, stats: dict, cluster_stats: dict) -> list
Description: Converts collected node stats into a list of Record objects for Prometheus export. Must now include records for the new "component_rss_bytes" and "component_uss_bytes" gauges. The "component_rss_bytes" record must always be emitted (unconditionally). The "component_uss_bytes" record must be emitted only when the accumulated USS value is greater than 0.0. The value stored in "component_uss_bytes" records must be the raw float bytes value (not divided by 1.0e6). The method must return exactly: 49 records when full stats with raylets are present; 46 records when stats["raylet"] is None; 55 records when GPU stats are included; 53 records when cluster_stats (autoscaler report) is empty. All existing references to METRICS_GAUGES["component_mem_shared_bytes"] must be changed to METRICS_GAUGES["component_shared_bytes"].


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.