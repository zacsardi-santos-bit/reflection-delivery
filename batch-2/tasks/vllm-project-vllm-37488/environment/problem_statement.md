## Description

The GPU model runner in vLLM's V1 worker stack doesn't properly integrate with the expert parallel load balancing (EPLB) subsystem during model loading and inference. When a mixture-of-experts model is loaded with real weights, the load balancing state should be initialized, the model registered with it, and the background rebalancing loop started. This isn't happening today, so the balancing subsystem remains idle even during active inference.

## Expected Behavior

- When loading a model with real weights, the model runner should register the loaded model with the load balancing state and start its background async loop.
- When loading with dummy weights (used for memory profiling), the communication buffer preparation and EPLB registration should be skipped — dummy loading should not trigger the balancing subsystem.
- A new method should allow rebuilding the load balancing state from an existing expert-to-layer assignment mapping, which is required for elastic expert parallelism workflows.
- During pipeline-parallel inference on stages that are not the final pipeline stage, the load balancing step should run after each batch is processed (after postprocessing), and the method should return without producing sampled tokens.

## Why This Matters

Without these integrations, the load balancing system can never activate, even when serving real mixture-of-experts workloads. Memory profiling with dummy weights would also unnecessarily trigger balancing initialization. Elastic expert parallelism — which requires rebuilding balancing state mid-serving — also becomes impossible without the mapping-based reconstruction method.
