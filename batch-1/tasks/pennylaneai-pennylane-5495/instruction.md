Implement a fully functional qutrit mixed-state simulation device for PennyLane, capable of executing circuits and returning valid results. Ensure it supports shot-based measurements, automatic differentiation, and integrates with PennyLane's execution tracker. Additionally, implement a function to check the validity of observables, including composite types.

*   Implement the `observable_stopping_condition` function in `pennylane/devices/default_qutrit_mixed.py`:
    *   Accept a single operator argument.
    *   Return `True` for GellMann operators.
    *   Return `False` for gate operators like TShift or non-observable operations like Snapshot.
    *   Recursively handle composite observables: return `True` for Tensor, Prod, Sum, and SProd if all sub-observables pass; for Hamiltonian/LinearCombination if all term observables pass.

*   Implement the `DefaultQutritMixed` class in `pennylane/devices/default_qutrit_mixed.py`:
    *   Decorate with `@simulator_tracking` and `@single_tape_support` in that order.
    *   Register as 'default.qutrit.mixed' in `setup.py`.
    *   Export from `pennylane.devices`.
    *   Ensure `shots` and `wires` properties are read-only, raising `AttributeError` on assignment.
    *   Initialize `_debugger` to `None`.

*   Implement the following methods in `DefaultQutritMixed`:
    *   `supports_derivatives(execution_config=None, circuit=None) -> bool`:
        *   Return `True` for `execution_config` as `None` or gradient methods 'backprop'/'best', if `circuit` is `None` or has no shots.
        *   Return `False` for other gradient methods and when `circuit` uses shots.
    *   `supports_jvp(execution_config=None, circuit=None) -> bool`: Always return `False`.
    *   `supports_vjp(execution_config=None, circuit=None) -> bool`: Always return `False`.
    *   `execute(circuits, execution_config=DefaultExecutionConfig) -> Result_or_ResultBatch`:
        *   Return actual simulation results.
        *   Support expval, sample, counts, probs measurements, shot vectors, batch tapes, and custom wire labels.
        *   Support backpropagation differentiation through major ML frameworks.
        *   Ensure reproducibility with integer seeds and JAX PRNGKeys.

*   Ensure tracker integration:
    *   Each device instance has a distinct tracker object.
    *   When inactive, tracker totals and history remain empty.
    *   When active, tracker records 'batches', 'executions', 'simulations', 'results', 'resources', 'errors', and 'shots'.
    *   Log exactly 3 records when debug logging is enabled during QNode execution.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.