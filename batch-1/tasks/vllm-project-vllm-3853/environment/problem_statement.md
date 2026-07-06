## Description

The current scheduler processes each request's entire input prompt in one scheduling pass before any output tokens can be generated for it. This means that when a large prompt is queued, it monopolizes the entire token budget for one or more rounds, blocking all other queued requests from making forward progress on output generation. There is no way to interleave prompt processing with output generation across requests.

We need a "chunked prefill" mode where long prompts are broken into smaller pieces and processed over multiple scheduling rounds, allowing decode steps (output generation) for other requests to run in the same batch alongside partial prompt processing.

## Expected Behavior

- A configuration option enables chunked prefill scheduling.
- When enabled, prompts longer than the per-batch token budget are chunked into pieces that fit within the budget, with each piece processed in successive scheduling rounds.
- Decode (output generation) requests and partially-processed prefill requests can be batched together in the same round.
- Decoding requests are prioritized over new prefill requests; partially-processed prefill requests take priority over brand-new prefill requests.
- When a partially-processed request is preempted due to memory pressure (and has only one active sequence), it is re-queued for recomputation rather than swapped out.
- The scheduler output reports how many prefill groups are included in each batch, and each scheduled group carries the count of tokens being processed in that batch.
- Requests whose prompt length exceeds the maximum model length are rejected and reported separately.

## Related Changes

- The scheduling budget tracker must be redesigned so that token and sequence counts are tracked per-request. Adding the same request to the budget twice must be a no-op, and removing a request that was not added must also be a no-op.
- Each sequence must carry an explicit stage indicating whether it is still processing its input prompt or generating output tokens. This stage must reset correctly when a sequence is preempted and re-queued for recomputation.
- The method that schedules currently-running sequences must be updated so that its output separately lists sequences in the decoding stage and sequences in the chunked-prefill stage.

## Why This Matters

Without chunked prefill, large prompts create "head-of-line blocking" where other requests stall waiting for a long prompt to fully process. Chunked prefill improves tail latency and allows the system to keep GPUs busy with a mix of prefill and decode work.
