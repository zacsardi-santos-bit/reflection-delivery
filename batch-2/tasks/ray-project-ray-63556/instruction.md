I'm working on Ray Serve's controller health monitoring and I've run into two gaps I'd like to address.

*   ControllerHealthMetrics and DurationStats must be importable from ray.serve.schema (not only from ray.serve._private.controller_health_metrics_tracker).

*   ControllerHealthMetrics must have a last_control_loop_time field of type float with a default value of 0.0.

*   ControllerHealthMetrics.model_dump() must include last_control_loop_time as a key in its output.

*   ControllerHealthMetricsTracker must expose a last_control_loop_time attribute (float, default 0.0) that can be read and written externally.

*   ControllerHealthMetricsTracker.collect_metrics() must return a ControllerHealthMetrics instance whose last_control_loop_time matches the tracker's current last_control_loop_time attribute value.

*   ServeInstanceDetails must have a controller_health_metrics field of type ControllerHealthMetrics. The default value must be a ControllerHealthMetrics instance with timestamp=0.0, num_control_loops=0, and last_control_loop_time=0.0.

*   ServeInstanceDetails._get_user_facing_json_serializable_dict(exclude_unset=True) must include controller_health_metrics as a key. The serialized controller_health_metrics dict must include at minimum the keys: timestamp, controller_start_time, uptime_s, num_control_loops, last_control_loop_time.

*   The full JSON representation of ServeInstanceDetails must be JSON-serializable end-to-end and must include controller_health_metrics as a top-level key.

*   The keys present in controller_health_metrics in the ServeInstanceDetails JSON must match the keys returned by the controller's get_health_metrics remote method.


*   Interface details: Type: Class
Name: ControllerHealthMetrics
Location: python/ray/serve/schema.py
Description: Pydantic model representing a snapshot of controller health metrics. Must be importable from ray.serve.schema. Must include a last_control_loop_time field (float, default 0.0) alongside existing fields (timestamp, controller_start_time, uptime_s, num_control_loops, loop_duration_s, loops_per_second, last_sleep_duration_s, event_loop_delay_s, num_asyncio_tasks, etc.). model_dump() output must include last_control_loop_time.
Signature: ControllerHealthMetrics(timestamp=0.0, controller_start_time=0.0, uptime_s=0.0, num_control_loops=0, last_control_loop_time=0.0, ...)

Type: Class
Name: DurationStats
Location: python/ray/serve/schema.py
Description: Pydantic model representing duration statistics. Must be importable from ray.serve.schema (in addition to or instead of from ray.serve._private.controller_health_metrics_tracker).

Type: Class
Name: ControllerHealthMetricsTracker
Location: python/ray/serve/_private/controller_health_metrics_tracker.py
Description: Tracks controller health metrics across control loop iterations. Must expose a last_control_loop_time attribute (float, publicly readable and writable, default 0.0). The collect_metrics() method must return a ControllerHealthMetrics whose last_control_loop_time field equals the tracker's current last_control_loop_time attribute.
Signature: collect_metrics() -> ControllerHealthMetrics

Type: Class
Name: ServeInstanceDetails
Location: python/ray/serve/schema.py
Description: Pydantic model for the full serve instance state. Must include a controller_health_metrics field of type ControllerHealthMetrics with a default of ControllerHealthMetrics() (all fields zeroed). _get_user_facing_json_serializable_dict(exclude_unset=True) must include controller_health_metrics in its output. The serialized controller_health_metrics dict must contain at minimum: timestamp, controller_start_time, uptime_s, num_control_loops, last_control_loop_time.
Signature: _get_user_facing_json_serializable_dict(exclude_unset: bool = False) -> dict


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.