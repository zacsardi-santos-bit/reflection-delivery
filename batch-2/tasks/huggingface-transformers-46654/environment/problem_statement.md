## Description

The attention mask creation helper for the diffusion decoder currently requires callers to pass a mask sized for the full static cache capacity, including all unfilled/empty cache slots. This means users must manually zero out the unfilled positions when working with a pre-allocated static cache, and must also know to extract the inner text configuration rather than passing the top-level configuration object directly.

This API is confusing and error-prone. It forces users to understand internal cache layout details that should be hidden from them.

## Expected Behavior

- The attention mask passed to the decoder mask creation function should cover only the actual input tokens (the tokens that were genuinely processed and stored in the cache), plus the canvas tokens. Users should not need to include or zero out unfilled cache capacity in the mask.
- The top-level model configuration object should be accepted directly by the mask creation function — callers should not need to manually extract a sub-configuration.
- The function should internally derive the positions of empty/unfilled cache slots and handle them correctly without requiring the caller to do so.

## Why This Matters

When using compiled (static cache) forward passes for diffusion generation, developers currently have to construct a correctly-sized mask that spans the entire pre-allocated cache, including empty positions. This is an unnecessary implementation detail that leaks into the public API. Simplifying the expected mask shape makes the interface more intuitive and reduces the chance of users passing incorrectly constructed masks.
