Update the `WindowEvent` enum in `src/event.rs` to rename gesture-related event variants to reflect the gesture action rather than the hardware. Ensure that the fields remain unchanged, and remove any references to specific hardware in the variant names.

*   Implement a `PinchGesture` variant in the `WindowEvent` enum:
    *   Fields: `device_id` of type `DeviceId`, `delta` of type `f64`, `phase` of type `TouchPhase`.
    *   Replace the existing `TouchpadMagnify` variant with `PinchGesture`.
    *   Ensure positive `delta` values indicate magnification and negative values indicate shrinking.

*   Implement a `DoubleTapGesture` variant in the `WindowEvent` enum:
    *   Field: `device_id` of type `DeviceId`.
    *   Replace the existing `SmartMagnify` variant with `DoubleTapGesture`.

*   Implement a `RotationGesture` variant in the `WindowEvent` enum:
    *   Fields: `device_id` of type `DeviceId`, `delta` of type `f32`, `phase` of type `TouchPhase`.
    *   Replace the existing `TouchpadRotate` variant with `RotationGesture`.
    *   Ensure positive `delta` values indicate counterclockwise rotation and negative values indicate clockwise rotation.

*   Maintain accessibility of the `TouchPhase` enum via the event module:
    *   Ensure it continues to expose at least a `Started` variant.

*   Maintain accessibility of the `DeviceId` type via the event module:
    *   Ensure it exposes a `dummy()` constructor callable in an unsafe context.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.