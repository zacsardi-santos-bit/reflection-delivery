I'm working on the terminal UI for an AI coding assistant and there's a problem with how the status command renders its output in the terminal history.

*   The AppEvent::RefreshRateLimits variant must carry an `origin: RateLimitRefreshOrigin` field instead of a bare `request_id: u64` field. When the /status command triggers a refresh, the origin must be RateLimitRefreshOrigin::StatusCommand { request_id }.

*   The AppEvent::RateLimitsLoaded variant must similarly carry `origin: RateLimitRefreshOrigin` instead of `request_id: u64`, forwarding the same origin that was set on the corresponding RefreshRateLimits event.

*   RateLimitRefreshOrigin must be a pub(crate) enum defined in codex-rs/tui/src/app_event.rs with at least two variants: StartupPrefetch (no fields) and StatusCommand { request_id: u64 }.

*   When a /status command is dispatched, the rendered status card inserted into terminal history must NOT contain the text 'refreshing limits'. Status history cards must be static and never show transient refresh notices.

*   After finish_status_rate_limit_refresh is called and rate limit data is cached, a subsequent dispatch of the /status command must produce a rendered output that reflects the cached data. For example, if the cached snapshot indicates 92% usage, the rendered output must contain '8% left'.

*   The ChatWidget struct must have a field named `refreshing_status_outputs` that is a collection tracking pending rate-limit refresh requests tied to status outputs. After finishing one of two concurrent refreshes, refreshing_status_outputs.len() must equal 1; after finishing both, refreshing_status_outputs.is_empty() must return true.

*   The rate_limit_snapshot_display function must accept a RateLimitSnapshot reference and a DateTime<Local> timestamp, and return a display value usable by new_status_output_with_rate_limits.

*   The new_status_output_with_rate_limits function must accept a `refreshing_rate_limits: bool` parameter (its last positional parameter). When this parameter is true and the provided rate limit snapshot has all-None fields (no displayable data), the rendered output must show 'not available for this account' rather than any refreshing-related text.

*   StatusRateLimitData must include an Unavailable variant (in addition to Available, Stale, and Missing). When a rate-limit response returns no displayable rows, the data must be treated as Unavailable. The rendered Limits row for Unavailable must display 'not available for this account'.

*   The existing snapshot for a status card with no rate limit data must render the Limits row as 'not available for this account' (replacing the old 'data not available yet' message). A new snapshot test for the case where refreshing_rate_limits is true but data is empty must produce the same 'not available for this account' output.


*   Interface details: Type: Enum
Name: RateLimitRefreshOrigin
Location: codex-rs/tui/src/app_event.rs
Description: Distinguishes why a rate-limit refresh was requested so the completion handler can route the result correctly. Must be pub(crate), derive Debug, Clone, Copy, PartialEq, Eq. Has two variants: StartupPrefetch (no fields) and StatusCommand { request_id: u64 }.

Type: Enum Variant
Name: AppEvent::RefreshRateLimits
Location: codex-rs/tui/src/app_event.rs
Description: Event that triggers a background rate-limit fetch. Must carry `origin: RateLimitRefreshOrigin` instead of the old `request_id: u64` field.
Signature: RefreshRateLimits { origin: RateLimitRefreshOrigin }

Type: Enum Variant
Name: AppEvent::RateLimitsLoaded
Location: codex-rs/tui/src/app_event.rs
Description: Event that delivers the result of a background rate-limit fetch. Must carry `origin: RateLimitRefreshOrigin` instead of the old `request_id: u64` field.
Signature: RateLimitsLoaded { origin: RateLimitRefreshOrigin, result: Result<Vec<RateLimitSnapshot>, String> }

Type: Enum Variant
Name: StatusRateLimitData::Unavailable
Location: codex-rs/tui/src/status/rate_limits.rs
Description: A new variant of StatusRateLimitData representing a completed refresh that returned no displayable usage data. When this variant is active, the rendered Limits row must display "not available for this account". This variant is returned by compose_rate_limit_data_many when the computed rows list is empty (previously returned Available(vec![])).

Type: Function
Name: rate_limit_snapshot_display
Location: codex-rs/tui/src/status/ (exact file in the status module)
Description: Converts a RateLimitSnapshot and a capture timestamp into a display value that can be passed to new_status_output_with_rate_limits.
Signature: rate_limit_snapshot_display(snapshot: &RateLimitSnapshot, captured_at: DateTime<Local>) -> <display type>

Type: Function
Name: new_status_output_with_rate_limits
Location: codex-rs/tui/src/status/ (exact file in the status module)
Description: Constructs a status output composite that includes rate-limit display data. The last positional parameter is `refreshing_rate_limits: bool`. When this is true and the provided rate limit snapshots yield no displayable rows, the output must render "not available for this account" in the Limits row (not any "refreshing" text).
Signature: new_status_output_with_rate_limits(config, account_display, token_info, usage, ..., rate_displays: &[<display type>], ..., refreshing_rate_limits: bool) -> <status output type>

Type: Field
Name: refreshing_status_outputs
Location: codex-rs/tui/src/chatwidget.rs (on ChatWidget struct)
Description: A public (test-accessible) collection field on ChatWidget that tracks pending rate-limit refresh requests tied to status command outputs. Must support .len() and .is_empty(). After finishing one of two concurrent refreshes, .len() must equal 1. After finishing all refreshes, .is_empty() must return true.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.