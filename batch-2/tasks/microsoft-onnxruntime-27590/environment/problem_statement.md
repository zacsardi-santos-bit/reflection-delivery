## Description

Qwen3 models compute rotary positional embeddings dynamically at inference time rather than using precomputed tables. During export, this produces a multi-node subgraph: learned inverse frequencies are multiplied with position IDs via matrix multiplication, transposed, concatenated with themselves, passed through cosine and sine functions, scaled, and unsqueezed to add a head dimension. The ONNX Runtime transformer optimizer for Qwen3 does not currently recognize this on-the-fly pattern, so it leaves all these individual nodes in the optimized graph instead of fusing them into the specialized fused rotary embedding operator already used for other models.

Additionally, some Qwen3 export variants include an extra expansion step (involving conditional selection) in the inverse-frequency path before the matrix multiplication. This variant also goes unrecognized by the current optimizer.

## Expected Behavior

- The Qwen3 optimizer should detect the on-the-fly rotary embedding computation pattern and replace it with fused rotary embedding nodes — one for the query path and one for the key path, for a total of 2 fused nodes in a single decoder layer.
- The optimizer should handle the variant where inverse frequencies are explicitly expanded before the matrix multiplication and still produce 2 fused nodes.
- When the inverse frequency values are not available as constants at optimization time (because they are provided as runtime inputs), the optimizer should gracefully skip fusion rather than crashing, leaving 0 fused rotary embedding nodes.
- After fusion, the model should contain precomputed cosine and sine cache tables as initializers. Their values must be computed from the inverse frequencies and the attention scaling factor using the standard trigonometric formula, verified to numerical precision across a wide range of sequence positions.

## Why This Matters

Without this fusion, Qwen3 models miss significant inference-time performance gains from the fused rotary embedding operator. The multi-step on-the-fly computation remains as a large number of individual nodes rather than a single optimized kernel, increasing memory bandwidth and latency during inference.
