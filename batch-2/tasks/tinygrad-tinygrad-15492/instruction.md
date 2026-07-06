I'm working with the operation graph optimizer and running into a case where conditional selection nodes aren't being eliminated as expected.

*   When a conditional selection (WHERE) chooses between a gated memory load and a zero fallback, and the WHERE condition is a superset of the load's validity condition (the WHERE condition contains all of the load's gate conditions plus additional extra conditions), the UOp graph optimizer must eliminate the WHERE node and combine all conditions into the load's gate.

*   The WHERE elimination must work regardless of whether the gated load appears in the true branch or the false branch of the conditional selection.

*   When the conditional selection result is subsequently cast to a different numeric type, the WHERE elimination must still occur and the type cast must be preserved in the resulting gated load expression.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.