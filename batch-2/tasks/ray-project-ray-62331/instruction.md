I'm working on an autoscaling system where scaling decisions include configurable delay periods before a scale-up or scale-down actually fires.

*   The _apply_delay_logic function must accept an optional _now parameter (a float representing the current wall-clock timestamp). When _now is provided it is used as the current time; when omitted, the function must call time.time() via a module-level 'import time' statement so that the time module can be patched in tests at 'ray.serve.autoscaling_policy.time'.

*   The delay logic inside _apply_delay_logic must use wall-clock elapsed time (comparing timestamps) to decide when to fire a scaling decision, not an iteration counter. A wall-clock timestamp must be recorded in policy_state when a consistent scaling direction is first observed. The scaling action fires once (current_time - first_request_timestamp) >= the configured delay in seconds.

*   For upscale: consecutive calls with _now values such that elapsed time < upscale_delay_s must return the current target replica count unchanged. A call where elapsed time >= upscale_delay_s must return the desired scaled-up replica count.

*   For downscale (multi-replica to one): consecutive calls where elapsed time < downscale_delay_s must return the current target unchanged. A call where elapsed time >= downscale_delay_s must return the downscaled count (e.g. 1).

*   For downscale-to-zero: consecutive calls where elapsed time < downscale_to_zero_delay_s must return 1. A call where elapsed time >= downscale_to_zero_delay_s must return 0.

*   When a scaling decision is interrupted by a decision in the opposite direction, the accumulated delay timer (timestamp) must be reset so that the full configured delay must elapse again before the original direction fires.

*   Regression: when control loop steps take longer than the standard interval (e.g. 600ms per iteration vs 100ms standard), the upscale must fire after approximately upscale_delay_s of real elapsed time. With upscale_delay_s=100s and actual_iteration_s=0.6s, the upscale must fire when simulated elapsed time is in [100s, 100s + 0.6s], and the overshoot factor (actual_elapsed / upscale_delay_s) must be less than 1.1.

*   The wall-clock timestamp stored in policy_state must be propagated correctly through the internal state-management helper functions (_apply_default_params_and_merge_state and _merge_user_state_with_internal_state). These helpers extract specific keys from policy_state before passing to _apply_delay_logic; they must be updated to include the new timestamp key so that the timestamp is not lost between successive policy calls. Without this, the timestamp resets on every invocation and the delay never fires.

*   The autoscaling_policy module must import the time module at module scope using 'import time' (not 'from time import time'), so that patching 'ray.serve.autoscaling_policy.time' replaces the module reference used inside _apply_delay_logic.


*   Interface details: Type: Function
Name: _apply_delay_logic
Location: python/ray/serve/autoscaling_policy.py
Signature: _apply_delay_logic(desired_num_replicas: int, curr_target_num_replicas: int, config: AutoscalingConfig, policy_state: dict, _now: Optional[float] = None) -> Tuple[int, dict]
Description: Applies upscale/downscale delay logic using wall-clock timestamps. Returns a tuple of (decision_num_replicas, updated_policy_state). The optional _now parameter accepts a float timestamp to override the current time (used in tests); when omitted, the function calls time.time() using the time module imported at module scope. The policy_state dict is used to persist a timestamp (recording when the current scaling direction was first observed) across successive calls.

Type: Function
Name: _apply_default_params_and_merge_state
Location: python/ray/serve/autoscaling_policy.py
Description: Internal helper that extracts delay-related state keys (including the new wall-clock timestamp key) from the incoming policy_state dict, passes them to _apply_delay_logic, and returns the updated state. This function must be updated to propagate whatever key name is used to store the scaling-direction timestamp in policy_state, so that the timestamp survives across successive policy calls. Without this update, the timestamp is dropped and the wall-clock delay logic resets on every call.

Type: Function
Name: _merge_user_state_with_internal_state
Location: python/ray/serve/autoscaling_policy.py
Description: Internal helper used by custom autoscaling policies to merge user-defined policy_state with internal delay state. Like _apply_default_params_and_merge_state, this function must be updated to include the wall-clock timestamp key so that the timestamp is not lost between calls when a custom policy is in use.

Module-level import requirement:
The autoscaling_policy module must use "import time" (not "from time import time") so that the time module reference at "ray.serve.autoscaling_policy.time" can be patched in tests. The function must call time.time() to get the current wall-clock time when _now is not provided.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.