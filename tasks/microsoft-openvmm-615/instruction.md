Implement a mesh-based channel library that allows message types to be used without requiring thread-safety, provided they are serializable. Update the channel's sender and receiver to work with any type that implements the serialization trait, regardless of thread-safety.

*   Update the `channel` function in `support/mesh/mesh_channel_core/src/mpsc.rs`:
    *   Ensure it accepts a type parameter `T` that implements `MeshField`.
    *   Remove any requirement for `T` to implement `Send`.
    *   Ensure the function signature is: `channel<T: MeshField>() -> (Sender<T>, Receiver<T>)`.

*   Modify the `Sender` struct in `support/mesh/mesh_channel_core/src/mpsc.rs`:
    *   Ensure `Sender<T>` works with `T: MeshField` without requiring `T: Send`.
    *   Implement the `send(value: T)` method to accept non-Send message types.
    *   Ensure `From<Port>` for `Sender<T>` and `From<Sender<T>>` for `Port` conversions work with `T: MeshField`.

*   Modify the `Receiver` struct in `support/mesh/mesh_channel_core/src/mpsc.rs`:
    *   Ensure `Receiver<T>` works with `T: MeshField` without requiring `T: Send`.
    *   Implement the `Stream` trait to allow asynchronous receiving via `.next().await`.
    *   Ensure `From<Port>` for `Receiver<T>` and `From<Receiver<T>>` for `Port` conversions work with `T: MeshField`.

*   Ensure the following conversions and operations:
    *   Converting a `Receiver<T>` to a `Port` and back should work for non-Send types.
    *   Sending a non-Send, serializable type through `Sender<T>::send()` should allow receiving the original value through `Receiver<T>`.
    *   Remove any `T: Send` or `T: 'static` bounds from `Sender<T>` and `Receiver<T>` and their trait implementations, relying solely on `T: MeshField`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.