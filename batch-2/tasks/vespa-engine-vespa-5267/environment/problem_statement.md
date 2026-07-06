## Description

The TensorFlow model importer cannot correctly handle neural network layers that use certain activation functions, particularly those involving broadcasting — where a small scalar or constant is applied element-wise across a larger multi-dimensional tensor. A common example is leaky ReLU, which multiplies each element by a small constant and takes the element-wise maximum with the original value.

When such a model is imported, two issues occur:
1. The importer stops at the wrong output node (an intermediate layer rather than the actual final activation output).
2. For element-wise operations between tensors of different ranks, the operands are placed in the wrong order, and the required dimension-reduction step for the smaller tensor is omitted.

## Expected Behavior

- The importer should follow the model's computation graph to its true final output node, even when that node is an element-wise maximum.
- For element-wise join operations between a larger-ranked tensor and a smaller-ranked one (broadcasting), the larger tensor must come first in the generated expression.
- When the smaller tensor contains dimensions of size 1 that would be broadcast against the larger tensor's dimensions, those size-1 dimensions must be reduced (via summation) before the join operation is applied.
- The resulting expression must produce numerical results equal to the original TensorFlow model.

## Why This Matters

Neural networks commonly use activation functions that require broadcasting, such as leaky ReLU. Without this fix, such models produce incorrect ranking expressions during import, causing them to fail at evaluation time or produce wrong results. This blocks users from deploying a significant class of trained neural network models in Vespa.
