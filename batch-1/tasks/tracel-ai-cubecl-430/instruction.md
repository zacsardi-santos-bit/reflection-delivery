Refactor the autotune system in the cubecl runtime to separate operation definitions from input data. Implement a more flexible API that allows for reusable operation sets, supports closures as tunable operations, and provides customizable checksum logic for cache invalidation.

*   Implement the `Tunable` trait:
    *   Define associated types `Inputs` and `Output`.
    *   Implement the `execute` method with the signature: `fn execute(&self, inputs: Self::Inputs) -> Result<Self::Output, AutotuneError>`.
    *   Ensure types implementing `Tunable` are `Clone`.

*   Develop the `TunableSet` struct:
    *   Construct using `fn new(key_fn: impl Fn(&Inputs) -> Key, clone_fn: impl Fn(&Key, &Inputs) -> Inputs) -> Self`.
    *   Provide `with_tunable` method: `fn with_tunable(self, tunable: impl Tunable<Inputs=Inputs, Output=Output>) -> Self`.
    *   Provide `with_custom_checksum` method: `fn with_custom_checksum(self, checksum_fn: impl Fn(&_) -> String) -> Self`.

*   Extend functionality with `AsFunctionTunable` trait:
    *   Implement `fn ok(self) -> impl Tunable` for closures/functions, allowing them to be used as tunables without manual `Tunable` implementation.

*   Update `LocalTuner::execute` method:
    *   Modify signature to `fn execute(&self, key: &K, client: &ComputeClient<S, C>, set: &TunableSet<K, I, O>, inputs: I)`.
    *   Ensure inputs are passed separately and not bundled within `TunableSet`.

*   Preserve caching behavior:
    *   Ensure executing operations with the same key results in a cache hit.
    *   Ensure different keys or checksums result in a cache miss.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.