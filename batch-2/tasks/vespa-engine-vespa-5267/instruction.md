Implement a fix for the TensorFlow model importer to correctly handle neural network layers using activation functions that involve broadcasting, such as leaky ReLU. Ensure the importer follows the computation graph to the true final output node and correctly orders operands in broadcasting operations.

*   Update the importer to:
    *   Use the maximum node as the output expression root when the final output node is an element-wise maximum operation.
        *   Ensure the output expression name reflects the maximum node's path (e.g., 'outputs/Maximum').
    *   Place the larger-ranked tensor as the first argument in Vespa join expressions when a join operation involves tensors of different ranks.
    *   Reduce size-1 dimensions of the smaller tensor using a sum-reduction before applying the join operation.
        *   For example, represent a constant tensor with shape [1] as `reduce(constant(...), sum, <dim>)` when broadcasting against a multi-row tensor.

*   Specifically for the dropout test model with a leaky ReLU output layer:
    *   Ensure the imported output expression for signature output 'y' has the name 'outputs/Maximum'.
    *   The expression string must be: `'join(join(tf_macro_outputs_BiasAdd, reduce(constant(outputs_Const), sum, d1), f(a,b)(a * b)), tf_macro_outputs_BiasAdd, f(a,b)(max(a,b)))'`.

*   Verify that the numerical result of the imported expression matches the result from the original TensorFlow model on the same input.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.