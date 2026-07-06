I'm working on the analytics module that records guardian review events in our AI pipeline.

*   The `target_item_id` field on `GuardianReviewEventParams` must be changed from a required `String` to `Option<String>`, allowing it to be absent (None) when no specific item is being reviewed.

*   The `tool_call_count` field on `GuardianReviewEventParams` must be changed from a required `u64` to `Option<u64>`, allowing it to be absent (None) when the tool call count is not available.

*   The `retry_reason` field must be removed from `GuardianReviewEventParams`.

*   The `NetworkAccess` variant of `GuardianReviewedAction` must contain only two fields: `protocol` of type `NetworkApprovalProtocol` (from `codex_protocol::approvals`) and `port` of type `u16`. Fields `target` and `host` must be removed from this variant.

*   The `NetworkApprovalProtocol` type must be accessible from the `codex_protocol::approvals` module and must include an `Https` variant.

*   The following types must remain accessible from `crate::events` (i.e., `codex-rs/analytics/src/events.rs`): `GuardianReviewEventParams`, `GuardianApprovalRequestSource` (with a `DelegatedSubagent` variant), `GuardianReviewDecision` (with a `Denied` variant), `GuardianReviewTerminalStatus` (with a `TimedOut` variant), `GuardianReviewFailureReason`, and `GuardianReviewedAction`.


*   Interface details: Type: Struct
Name: GuardianReviewEventParams
Location: codex-rs/analytics/src/events.rs
Description: Holds the parameters for a guardian review analytics event. The `target_item_id` field must be `Option<String>` (was previously `String`). The `tool_call_count` field must be `Option<u64>` (was previously `u64`). The `retry_reason` field must be removed entirely. All other fields remain the same.
Signature:
  pub struct GuardianReviewEventParams {
      pub thread_id: String,
      pub turn_id: String,
      pub review_id: String,
      pub target_item_id: Option<String>,
      pub approval_request_source: GuardianApprovalRequestSource,
      pub reviewed_action: GuardianReviewedAction,
      pub reviewed_action_truncated: bool,
      pub decision: GuardianReviewDecision,
      pub terminal_status: GuardianReviewTerminalStatus,
      pub failure_reason: Option<GuardianReviewFailureReason>,
      pub risk_level: Option<...>,
      pub user_authorization: Option<...>,
      pub outcome: Option<...>,
      pub guardian_thread_id: Option<String>,
      pub guardian_session_kind: Option<...>,
      pub guardian_model: Option<String>,
      pub guardian_reasoning_effort: Option<String>,
      pub had_prior_review_context: Option<bool>,
      pub review_timeout_ms: u64,
      pub tool_call_count: Option<u64>,
      pub time_to_first_token_ms: Option<u64>,
      pub completion_latency_ms: Option<u64>,
      pub started_at: u64,
      pub completed_at: Option<u64>,
      pub input_tokens: Option<u64>,
      pub cached_input_tokens: Option<u64>,
      pub output_tokens: Option<u64>,
      pub reasoning_output_tokens: Option<u64>,
      pub total_tokens: Option<u64>,
  }

Type: Enum
Name: GuardianReviewedAction
Location: codex-rs/analytics/src/events.rs
Description: Represents the action that was submitted for guardian review. The `NetworkAccess` variant must be updated to contain only `protocol: NetworkApprovalProtocol` and `port: u16`. The fields `target` and `host` must be removed from this variant.
Signature:
  pub enum GuardianReviewedAction {
      // ... other variants unchanged ...
      NetworkAccess {
          protocol: NetworkApprovalProtocol,
          port: u16,
      },
      // ...
  }

Type: Enum
Name: NetworkApprovalProtocol
Location: codex-rs/protocol/src/approvals.rs  (accessible as codex_protocol::approvals::NetworkApprovalProtocol)
Description: Represents the network protocol for a network access action. Must include an `Https` variant.
Signature:
  pub enum NetworkApprovalProtocol {
      Https,
      // ... other variants ...
  }

Type: Enum
Name: GuardianApprovalRequestSource
Location: codex-rs/analytics/src/events.rs
Description: Represents the source that initiated the guardian approval request. Must include a `DelegatedSubagent` variant.

Type: Enum
Name: GuardianReviewDecision
Location: codex-rs/analytics/src/events.rs
Description: Represents the decision outcome of a guardian review. Must include a `Denied` variant.

Type: Enum
Name: GuardianReviewTerminalStatus
Location: codex-rs/analytics/src/events.rs
Description: Represents the terminal status of a guardian review. Must include a `TimedOut` variant.

Type: Enum
Name: GuardianReviewFailureReason
Location: codex-rs/analytics/src/events.rs
Description: Represents the reason for a guardian review failure. Used as `Option<GuardianReviewFailureReason>` in GuardianReviewEventParams.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.