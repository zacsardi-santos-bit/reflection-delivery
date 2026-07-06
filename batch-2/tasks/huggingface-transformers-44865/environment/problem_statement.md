## Description

Some model architectures in this library support distributing the forward pass across multiple compute devices using pipeline parallelism. In this setup, submodules that are not assigned to a particular device are replaced with identity operations — they receive input and return it unchanged. If model forward methods read custom attributes from those submodules (for example, querying a layer's attention type to select the right mask), the code will raise an attribute error at runtime on any device where the submodule has been swapped out.

The correct pattern is to read per-layer metadata from the model configuration object using an index rather than from the submodule itself. However, there is currently nothing to detect and flag the unsafe pattern at code-review time.

## Expected Behavior

A new automated code quality rule should be added to the modeling structure checker that:

- Detects attribute accesses on submodules that are tracked by the pipeline parallelism plan — both direct accesses and accesses through loop variables — when the attribute would not exist on a plain identity module.
- Produces a violation with a message that names the unsafe access expression.
- Skips models that have not registered for pipeline parallelism.
- Allows access to attributes that exist on any standard neural network module (such as the training flag).
- Allows access via the model configuration object (e.g., reading layer type from the config).
- Supports suppression via an inline comment for cases where the access is known to be intentional.

## Why This Matters

Without this check, pipeline-parallel models silently compile with code that will crash on non-owning devices. Catching the pattern statically — before code is merged — prevents hard-to-debug runtime failures in distributed training setups.
