Update the `initialize` method of the `System` trait to return a `FilteredAccessSet<ComponentId>` instead of nothing. Ensure that this change allows for immediate conflict detection with other access sets. Implement this behavior for all systems, including those created from regular functions using the standard conversion mechanism.

*   Modify the `System` trait:
    *   Change the `initialize` method signature to:
        ```rust
        fn initialize(&mut self, world: &mut World) -> FilteredAccessSet<ComponentId>
        ```
    *   Ensure the method returns a `FilteredAccessSet<ComponentId>` representing the system's component access.

*   Implement conflict detection:
    *   Ensure the returned `FilteredAccessSet<ComponentId>` supports calling `get_conflicts` with another `FilteredAccessSet<ComponentId>`.

*   Update systems created via `IntoSystem::into_system`:
    *   Ensure they implement the updated `initialize` method signature that returns `FilteredAccessSet<ComponentId>`.

*   Verify that all implementors of the `System` trait match the updated `initialize` method signature.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.