I'm working on vLLM's V1 GPU model runner and the expert parallel load balancing (EPLB) system just isn't wired up right now, so even when I serve a real mixture-of-experts model the balancing subsystem sits idle during inference. I need to fix that in a few spots.

First, the model loading path. When I load a model with real weights I want the runner to create the load balancing state, register the loaded model with it, and kick off the background async rebalancing loop. That's the whole point, without it EPLB can never activate on real MoE workloads. But when I load with dummy weights (which is what happens during GPU memory profiling), all of that should be skipped, no EPLB registration and no background loop, and the load format should get set to the dummy value before loading begins. Same deal with the communication buffer prep, that should only run on the real-weights path, not the dummy one. It's wasteful to spin up balancing init just to profile memory.

I also need a new method that rebuilds the load balancing state from an existing expert assignment mapping, this is the thing elastic expert parallelism needs. It should take the mapping tensor plus the count of valid physical experts and reconstruct the balancing state from those two inputs along with the runner's existing config.

Last thing, pipeline parallel serving. On intermediate stages, the ones that aren't the final pipeline stage, I want the load balancing step to fire after each batch. Specifically once postprocessing finishes on a non-final stage, run the balancing step and then just return without any sampled output. Final stage keeps behaving normally.

All of this lives in the V1 GPU model runner under `@vllm/v1/worker/gpu_model_runner.py`.
