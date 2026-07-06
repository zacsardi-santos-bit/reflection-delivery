## Description

When debugging tensor comparison issues between two parallel model runs, it is difficult to understand what context surrounds the comparison results. Users have no visibility into which input token sequences were processed at each rank, how the model is distributed across ranks (e.g., tensor parallelism and pipeline parallelism topology), or what alignment plan was applied to the tensors before comparison. This makes it hard to diagnose whether differences are caused by mismatched inputs, incorrect sharding, or alignment issues.

Additionally, building alignment plans currently requires passing a parameter that has become unnecessary due to internal refactoring. Constructing plans without this parameter should be supported.

## Expected Behavior

- The comparison tool should display input token IDs and their positions for each rank at each step, along with the total number of tokens.
- When a tokenizer is available (either specified explicitly or auto-discovered from dump file metadata), the tool should also display a human-readable decoded version of the token IDs.
- The comparison tool should display the rank topology for each rank, including tensor parallelism and pipeline parallelism configuration in a "current rank / total" format.
- Comparison output records should embed the alignment plan that was used, so users can inspect how tensors were grouped and transformed. When rendered as text, the plan should list the type of each sub-operation (e.g., unsharding).
- Dump files should be able to store and expose the tokenizer path in their metadata, which the tool can discover automatically.
- Alignment plan constructors must work without specifying the deprecated token-dimension parameter.

## Why This Matters

Tensor comparison between distributed model runs is opaque without input context. Being able to see what sequences were fed to each rank and how the topology is configured makes it far easier to diagnose correctness issues in distributed inference.
