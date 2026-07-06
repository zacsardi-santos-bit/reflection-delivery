Implement two new quantum templates, OutAdder and ModExp, for out-of-place modular addition and modular exponentiation, respectively, in the PennyLane library. Ensure these templates are accessible from the main namespace and support just-in-time (JIT) compilation. Validate wire group overlaps and ensure sufficient wires are provided for the operations.

*   Implement the OutAdder class in `pennylane/templates/subroutines/out_adder.py`.
    *   Extend `pennylane.operation.Operation`.
    *   Export from `pennylane/templates/subroutines/__init__.py` as `qml.OutAdder`.
    *   Signature: `OutAdder(x_wires, y_wires, output_wires, mod=None, work_wires=None, id=None)`.
    *   Compute `(x + y + initial_output) % mod` and store in `output_wires`.
    *   Default `mod` to `2**len(output_wires)` if not provided.
    *   Raise `ValueError` with specific messages for wire overlap and insufficient wires.
    *   Implement `compute_decomposition` as a static method returning a list of quantum operations.
    *   Include a `_primitive` attribute and `_primitive_bind_call` classmethod for JAX support.

*   Implement the ModExp class in `pennylane/templates/subroutines/mod_exp.py`.
    *   Extend `pennylane.operation.Operation`.
    *   Export from `pennylane/templates/subroutines/__init__.py` as `qml.ModExp`.
    *   Signature: `ModExp(x_wires, output_wires, base, mod=None, work_wires=None, id=None)`.
    *   Compute `(initial_output * base^x) % mod` and store in `output_wires`.
    *   Default `mod` to `2**len(output_wires)` if not provided.
    *   Raise `ValueError` with specific messages for wire overlap and insufficient wires.
    *   Implement `compute_decomposition` as a static method returning a list of quantum operations.
    *   Include a `_primitive` attribute and `_primitive_bind_call` classmethod for JAX support.

*   Ensure both classes pass `qml.ops.functions.assert_valid` and are compatible with JAX JIT compilation.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.