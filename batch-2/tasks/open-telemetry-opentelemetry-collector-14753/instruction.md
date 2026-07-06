I've noticed that the OpenTelemetry Collector's internal self-monitoring metrics are using plural unit strings (like "records", "spans", "datapoints", "samples", etc.

*   All telemetry metric unit strings across Collector components must use singular form instead of plural form to conform to the OpenTelemetry specification.

*   The unit for metrics counting trigger-send events (e.g., batch size trigger sends, timeout trigger sends) must be "{time}" (not "{times}").

*   The unit for metrics counting queue capacity, queue length, and incoming/outgoing items must be "{item}" (not "{items}").

*   The unit for metrics counting log records (accepted, refused, failed, sent, enqueued) must be "{record}" (not "{records}").

*   The unit for metrics counting metric data points (accepted, refused, failed, sent, enqueued, scraped, errored) must be "{datapoint}" (not "{datapoints}").

*   The unit for metrics counting profile samples (accepted, refused, failed, sent, enqueued, scraped, errored) must be "{sample}" (not "{samples}").

*   The unit for metrics counting spans (accepted, refused, failed, sent, enqueued) must be "{span}" (not "{spans}").

*   The unit for metrics counting batch units in batch-send-size histograms must be "{unit}" (not "{units}").

*   The unit for metrics counting queue capacity and queue size (in batches) must be "{batch}" (not "{batches}").

*   The unit for metrics counting distinct metadata value combinations (metadata cardinality) must be "{combination}" (not "{combinations}").

*   The unit for metrics counting requests must be "{request}" (not "{requests}").

*   These unit changes must be reflected in the source metadata definition files for each component (receiver, exporter, processor, scraper), as well as in all generated telemetry registration code that passes the unit string to the metrics instrumentation API.


*   Interface details: NO INTERFACES NEEDED

The task requires updating existing unit string values in metadata definition files and generated telemetry registration code. No new functions, classes, or methods are introduced. The changes are purely to string constant values passed to existing metric registration APIs across multiple components.

The specific files and unit string mappings that must be updated are documented in requirements.json. All the relevant functions and methods already exist in the codebase; only their unit string arguments need to change from plural to singular form.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.