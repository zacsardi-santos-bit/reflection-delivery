Implement the necessary trait to allow structs with array-typed fields to be annotated with the CubeLaunch derive attribute without causing compilation errors. Ensure that the derive macro-generated code can handle these array fields correctly.

*   Implement the IntoRuntime trait for Array<E> where E: CubePrimitive.
    *   Provide an implementation for the method `fn __expand_runtime_method(self, _context: &mut CubeContext) -> Self::ExpandType`.
    *   Use `unimplemented!()` within the method body, as arrays cannot exist at compile time, but ensure the method is present for compilation.

*   Ensure the CubeLaunch derive attribute can be applied to structs with named fields of type Array.
    *   Support structs where the array element type is a concrete type, such as f32.
    *   Support generic structs where the array element type is parameterized by a generic float type parameter (F: Float).

*   Verify that the CubeLaunch derive macro-generated code correctly invokes the IntoRuntime trait method on each array-typed field value when producing the expand runtime implementation for the struct.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.