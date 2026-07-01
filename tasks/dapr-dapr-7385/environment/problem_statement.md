## Description

The Dapr workflow engine does not correctly support child workflows (sub-orchestrations). When a parent workflow attempts to invoke a nested child workflow, pass it input, and await its completion, the execution fails to complete successfully. This means developers cannot use hierarchical workflow patterns where a root workflow delegates work to a child workflow and uses the child's result.

## Expected Behavior

- A parent workflow should be able to invoke a child workflow by name and pass input to it
- The child workflow should receive the input, execute its logic, and return a result
- The parent workflow should successfully receive the child's output and use it as its own return value
- The entire nested execution should complete within a reasonable timeout
- Unicode input data should be passed through correctly and appear in the output

## Why This Matters

Hierarchical workflow composition is a fundamental pattern in workflow orchestration. Without working child workflow support, developers are forced to flatten all logic into a single workflow, losing the benefits of modular, reusable sub-workflows. Fixing this enables building complex, composable workflows where different concerns are handled by separate sub-workflows that communicate through inputs and outputs.
