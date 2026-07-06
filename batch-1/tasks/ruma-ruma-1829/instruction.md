Implement support for three formats in the `CallMemberEventContent` to handle legacy, per-device, and empty states. Refactor the call notification event tests to run inline with the module's code. Ensure all changes are compatible with the MatrixRTC protocol requirements.

*   Update `crates/ruma-events/Cargo.toml`:
    *   Declare `unstable-msc4075` as dependent on `unstable-msc3401`.

*   Refactor `CallMemberEventContent` in `crates/ruma-events/src/call/member.rs`:
    *   Convert from a struct to an enum with variants: `LegacyContent`, `SessionContent`, and `Empty`.
    *   Derive `PartialEq` and use `#[serde(untagged)]`.
    *   Implement methods: `new_legacy`, `new`, `new_empty`, `active_memberships`, `memberships`, and `set_created_ts_if_none`.
    *   Implement `RedactContent` with `Redacted = RedactedCallMemberEventContent`.

*   Define `LegacyMembershipContent`:
    *   Private field `memberships: Vec<LegacyMembershipData>` with JSON key `"memberships"`.

*   Define `SessionMembershipData` in `crates/ruma-events/src/call/member/member_data.rs`:
    *   Fields: `application`, `device_id`, `foci_preferred`, `focus_active`, `created_ts`.

*   Define `EmptyMembershipData`:
    *   Field: `leave_reason` with `#[serde(skip_serializing_if = "Option::is_none")]`.

*   Define `LeaveReason` enum:
    *   Variants: `LostConnection`, `_Custom`.

*   Define `MembershipData<'a>` enum:
    *   Variants: `Legacy`, `Session`.

*   Define `LegacyMembershipData`:
    *   Fields: `application`, `device_id`, `expires`, `created_ts`, `foci_active`, `membership_id`.

*   Define `Focus` enum in `crates/ruma-events/src/call/member/focus.rs`:
    *   Variant: `Livekit`.

*   Define `LivekitFocus`:
    *   Fields: `alias`, `service_url`.

*   Define `ActiveFocus` enum:
    *   Variant: `Livekit`.

*   Define `ActiveLivekitFocus`:
    *   Field: `focus_select`.

*   Define `FocusSelection` enum:
    *   Variant: `OldestMembership`.

*   Define `RedactedCallMemberEventContent` as a unit struct.

*   Create submodules `focus` and `member_data` in `crates/ruma-events/src/call/member.rs`:
    *   Declare with `mod focus; mod member_data;`.
    *   Re-export with `pub use focus::*; pub use member_data::*;`.

*   Move call notification event tests to `crates/ruma-events/src/call/notify.rs`:
    *   Add `notify_event_serialization` and `notify_event_deserialization` tests inside `#[cfg(test)] mod tests`.

*   Add/update tests in `crates/ruma-events/src/call/member.rs`:
    *   Tests: `serialize_legacy_call_member_event_content`, `deserialize_legacy_call_member_event_content`, `deserialize_member_event`, `memberships_do_expire`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.