Implement a new "binding" concept to manage memory resources more effectively in the compute server API. Convert memory handles into bindings and update the read and execute operations to accept these bindings by value. Ensure that the binding type is cloneable and exposes the underlying memory resource.

*   Define a new `Binding` type in the server module:
    *   Export `Binding` from `burn_compute::server`.
    *   Include a public `memory` field of type `MM::Binding`.
    *   Implement `Clone` for `Binding`.

*   Update the `Handle` type:
    *   Implement a method `binding(self) -> Binding<Server>` in `crates/burn-compute/src/server.rs`.
    *   Ensure it calls `MemoryHandle::binding()` on its inner memory field.

*   Modify the `ComputeServer` trait:
    *   Change `read` method signature to `fn read(&mut self, binding: Binding<Self>) -> Reader<Vec<u8>>`.
    *   Change `execute` method signature to `fn execute(&mut self, kernel: Self::Kernel, bindings: Vec<Binding<Self>>)`.

*   Update the `ComputeClient` methods:
    *   Change `read` method to accept `Binding<Server>` by value.
    *   Change `execute` method to accept `Vec<Binding<Server>>` by value.

*   Adjust the `MemoryHandle` trait:
    *   Make it generic over a `Binding` type parameter.
    *   Add a method `fn binding(self) -> Binding`.
    *   Export `MemoryHandle` from `burn_compute::memory_management`.

*   Introduce a `MemoryBinding` marker trait:
    *   Define `trait MemoryBinding: Clone + Send + Sync + Debug {}`.
    *   Ensure any binding type implements this trait.
    *   Export `MemoryBinding` from `burn_compute::memory_management`.

*   Update the `MemoryManagement` trait:
    *   Add a new associated type `Binding: MemoryBinding`.
    *   Rebound `Handle` as `Handle: MemoryHandle<Self::Binding>`.
    *   Change `get` method signature to `fn get(&mut self, binding: Self::Binding)`.

*   Ensure server implementations can:
    *   Access a binding's memory resource using `binding.memory`.
    *   Convert a raw memory handle to a binding using `handle.clone().binding()`.

*   Update the `ComputeChannel` trait:
    *   Modify `read` method to accept `Binding<Server>` by value.
    *   Modify `execute` method to accept `Vec<Binding<Server>>` by value.

*   Adjust autotune operation set types in integration tests:
    *   Ensure constructors accept `Vec<Binding<DummyServer>>`.
    *   Store and clone bindings internally.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.