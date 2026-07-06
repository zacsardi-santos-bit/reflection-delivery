Implement the necessary changes to ensure test isolation, name validation, and improved developer ergonomics for IR operators in the vLLM framework. Ensure each test operates with its own isolated registry and enforce strict naming conventions for operator and provider names. Capture stack traces during registration and provide meaningful string representations for operators.

*   Update `vllm/ir/op.py`:
    *   Rename the module-level torch Library instance to `vllm_ir_torch_lib`.
    *   Ensure `IrOp` and `IrOpInplaceOverload` dynamically resolve the `torch.ops` subtree using `vllm_ir_torch_lib.ns`.
    *   Implement `_validate_name(name: str, entity_type: str) -> None` to enforce names matching `[a-z_][a-z_0-9]*` and raise `ValueError` with a message matching `name.*invalid` for invalid names.
    *   Modify `IrOp`:
        *   Add `_registration_stack` to store traceback at registration time, ensuring the last entry is the user's decorator call.
        *   Implement `__repr__` to return `"IrOp('<name>')"`.
        *   Implement `__str__` to return `"IrOp('<name>')"` or `"IrOp('<name>') - <first line of docstring>"`.
    *   Modify `IrOpImpl` to include `_registration_stack` for traceback at registration time.
    *   Ensure `register_op` decorator:
        *   Validates op names using `_validate_name`.
        *   Captures stack trace at registration, slicing off internal frames.
        *   Passes the stack to `IrOp.__init__`.
    *   Ensure `register_impl` method on `IrOp`:
        *   Validates provider names using `_validate_name`.
        *   Captures stack trace at registration, slicing off internal frames.
        *   Passes the stack to `IrOpImpl.__init__`.

*   Update `tests/conftest.py`:
    *   Implement `fake_vllm_ir` fixture:
        *   Use `monkeypatch.setattr` to replace `IrOp.registry` with an empty dict.
        *   Replace `vllm.ir.op.vllm_ir_torch_lib` with a new `torch.library.Library` instance using a unique namespace.
        *   Ensure cleanup after each test to restore original states.

*   Ensure:
    *   Each test operates with its own isolated registry.
    *   Only valid names are accepted for registration.
    *   Stack traces are captured and available for debugging.
    *   Operators have meaningful string representations.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.