Implement support for noisy trajectory-based simulation in the quantum circuit expectation layer of TensorFlow Quantum. Allow users to specify the backend using string identifiers and manage the repetitions parameter for noisy simulations.

*   Update the `Expectation` class in `tensorflow_quantum/python/layers/circuit_executors/expectation.py` to support:
    *   `backend='noisy'`, `backend='noiseless'`, `backend=None`, and a cirq simulator object as valid backend values.
*   Modify the `call` method to include:
    *   An optional `repetitions` parameter for specifying the number of trajectories in noisy simulations.
    *   Raise an exception with the message containing 'repetitions not provided' if `backend='noisy'` and `repetitions` is not provided.
    *   Raise an exception with the message containing 'cannot be parsed' if `backend='noisy'` and `repetitions` cannot be converted to a valid integer tensor.
    *   Raise an exception with the message containing 'noiseless' if `backend='noiseless'` and `repetitions` is provided.
*   Ensure the `repetitions` parameter:
    *   Accepts a single integer, a 1D list, or a 2D list when `backend='noisy'`.
*   Ensure the `Expectation` layer supports:
    *   2D operator batches paired with a corresponding list of circuits when `backend='noisy'`.
    *   All existing input combinations: circuits alone, circuits with `symbol_names`, circuits with `symbol_names` and `symbol_values`, and their batched variants.
*   Verify that models using the `Expectation` layer with `backend='noisy'` and sufficient repetitions (e.g., 1000) are trainable and converge on simple single-qubit problems, albeit with a relaxed tolerance compared to the noiseless case.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.