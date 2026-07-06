Implement a simple planner, `ActionPlanner`, that selects the most relevant function from registered skills to achieve a given goal. Ensure it validates inputs and raises appropriate errors when necessary. This planner should be accessible from the main planning package.

Requirements:

*   Make `ActionPlanner` importable from `semantic_kernel.planning`.
    *   Ensure `from semantic_kernel.planning import ActionPlanner` is functional.
*   Implement the `ActionPlanner` class in `python/semantic_kernel/planning/action_planner/action_planner.py`.
    *   Constructor signature: `__init__(kernel: Kernel, prompt: Optional[str] = None, logger: Optional[Logger] = None) -> None`
    *   Raise `PlanningException` if the `kernel` argument is `None`.
    *   Raise `ValueError` if the provided `kernel` lacks a completion or chat service, triggered by `kernel.create_semantic_function`.
    *   Call `kernel.create_semantic_function(...)` and `kernel.create_new_context()` during construction.
*   Implement `create_plan_async(goal: str) -> Plan` method.
    *   Raise `PlanningException` if `goal` is an empty string.
    *   Raise `PlanningException` if the LLM response lacks a valid 'plan' key in the JSON structure.
    *   Return a `Plan` object with `.description` equal to the selected function's description.
    *   Ensure `Plan.state` contains parameters from the LLM-generated plan, accessible via `state.contains_key(key)`.
*   Implement `list_of_functions(goal: str, context: SKContext) -> str` method.
    *   Accept keyword arguments `goal` (str) and `context` (SKContext).
    *   Return a string listing each available function as `{skill_name}.{function_name}`.
    *   Use `context.skills.get_functions_view()` to retrieve available functions.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.