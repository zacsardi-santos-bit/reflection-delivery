Implement the `LegacyDeviceFacade` class in `pennylane/devices/legacy_facade.py` to wrap legacy devices and make them compatible with the new device interface. Ensure it exposes device properties, translates shot configurations, and supports gradient computation and debugging.

*   Implement `LegacyDeviceFacade` in `pennylane/devices/legacy_facade.py`.
    *   Export `LegacyDeviceFacade` from `pennylane/devices/__init__.py` as `qml.devices.LegacyDeviceFacade`.
*   Constructor (`__init__`) must:
    *   Raise `ValueError` with 'The LegacyDeviceFacade only accepts' if the device is not a `qml.devices.LegacyDevice`.
*   Implement properties:
    *   `target_device` returns the wrapped legacy device.
    *   `name` returns the legacy device's `short_name`.
    *   `shots` returns a `qml.measurements.Shots` object for the legacy device's shot configuration.
    *   `wires` returns the wires from the wrapped legacy device.
    *   Delegate `author`, `version`, and other attributes via `__getattr__`.
*   Implement `__repr__` to return '<LegacyDeviceFacade: {repr(legacy_device)}>'.
*   Implement `tracker` to delegate to the wrapped device's tracker.
    *   Ensure `qml.Tracker` records executions, shots, batches, and batch_len correctly.
*   Implement `preprocess(execution_config=DefaultExecutionConfig)`:
    *   Return a `TransformProgram` with transforms: `legacy_device_batch_transform`, `legacy_device_expand_fn`, and `qml.defer_measurements`.
    *   Exclude `qml.defer_measurements` if `supports_mid_measure=True`.
*   Implement `execute()`:
    *   Pass `postselect_mode` to `batch_execute` when mid-circuit measurements are supported.
*   Implement `legacy_device_expand_fn(tape, device)`:
    *   Wrap `expand_fn`, return a single-element tuple of tapes, and a postprocessing function returning `results[0]`.
*   Implement `legacy_device_batch_transform(tape, device)`:
    *   Wrap `batch_transform`, respecting shot count.
*   Implement `supports_derivatives(execution_config=None, circuit=None)`:
    *   Return `False` if no derivative support or if tape contains `SparseHamiltonian`.
*   Implement `_validate_adjoint_method(tape)`:
    *   Return `True` if conditions for adjoint differentiation are met.
*   Implement `_validate_device_method(tape)`:
    *   Return `True` if `provides_jacobian=True`.
*   Implement `_create_temp_device(batch)`:
    *   Return `target_device` or emit `qml.PennyLaneDeprecationWarning` if switching devices.
*   Handle errors:
    *   Raise `qml.DeviceError` for unsupported differentiation methods.
    *   Raise `qml.DeviceError` for unsupported backpropagation.
*   Ensure snapshot/debugging support:
    *   `qml.snapshots` should work through the facade for devices with debugger support.
*   Handle batch execution:
    *   Execute and differentiate each circuit separately, tracking shot counts individually.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.