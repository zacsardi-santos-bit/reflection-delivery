I'm working on the memory consolidation system and need to add a recency filter to the phase-2 input selection.

*   The get_phase2_input_selection method on StateRuntime must accept a second parameter max_unused_days (i64) in addition to the existing n (usize) parameter, so the signature becomes get_phase2_input_selection(n: usize, max_unused_days: i64).

*   When get_phase2_input_selection is called with a max_unused_days value, it must exclude memories whose last_usage falls outside that window; for memories with no last_usage recorded, it must fall back to source_updated_at to determine eligibility.

*   After phase2 dispatch reclaims a stale global lock and runs consolidation, a subsequent attempt to claim the global phase2 job must return either Phase2JobClaimOutcome::SkippedRunning (job still in progress) or Phase2JobClaimOutcome::SkippedNotDirty (job completed with nothing left to process). Both outcomes are valid.

*   Test fixtures that seed stage1 output for phase2 dispatch tests must use the current UTC Unix timestamp (not a hardcoded historical value) so seeded records are within the recency window enforced by max_unused_days filtering.


*   Interface details: Type: Method
Name: get_phase2_input_selection
Location: codex-rs/state/src/runtime/memories.rs
Signature: get_phase2_input_selection(&self, n: usize, max_unused_days: i64) -> anyhow::Result<Phase2InputSelection>
Description: Queries the state DB for up to n stage-1 outputs eligible for phase-2 consolidation. Eligibility is determined by max_unused_days: a memory is eligible if its last_usage is within max_unused_days days of now, or (if last_usage is NULL) its source_updated_at is within that window. Results are ordered by usage_count DESC, then by COALESCE(last_usage, source_updated_at) DESC, then source_updated_at DESC, then thread_id DESC.

Type: Enum
Name: Phase2JobClaimOutcome
Location: codex-rs/state/src/runtime/memories.rs (or nearby phase2 job module)
Description: Represents the result of attempting to claim the global phase-2 consolidation job. Must include at minimum the variants: SkippedRunning (a job is already in progress) and SkippedNotDirty (no new work is available). After phase2 dispatch reclaims a stale lock and runs consolidation, a subsequent try_claim_global_phase2_job call must return one of these two variants.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.