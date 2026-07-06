Implement the ability for a single content struct declaration in the Matrix event system to specify two compatible event categories at once, specifically GlobalAccountData and RoomAccountData. Extend the event content derive macro to support this dual-category declaration and ensure the system rejects incompatible combinations at compile time with a clear error message.

Requirements:

*   Extend the event content derive macro to accept a double-kind attribute syntax using '+' to combine two event kinds.
    *   Allow GlobalAccountData + RoomAccountData or RoomAccountData + GlobalAccountData in the `ruma_event` attribute.
*   For a content struct declared with both GlobalAccountData and RoomAccountData kinds:
    *   Implement both the GlobalAccountDataEventContent and RoomAccountDataEventContent traits for the struct.
    *   Ensure each trait's `event_type()` method returns the correct event type string.
    *   Generate two event type aliases within the same module:
        *   Use 'Global' and 'Room' prefixes followed by the PascalCase form of the event type name and 'Event'.
        *   Example: For event type 'm.macro.test', generate GlobalMacroTestEvent and RoomMacroTestEvent.
*   Update the `event_enum!` macro:
    *   When listing the same event type path in both a GlobalAccountData and a RoomAccountData enum block, generate correctly typed enum variants using the prefixed type aliases (GlobalXxxEvent for global, RoomXxxEvent for room).
    *   Ensure both GlobalAccountDataEventType and RoomAccountDataEventType enums include a variant for the event type, with `to_string()` returning the event type string (e.g., 'm.macro.test').
*   Ensure compilation fails with the exact error message "only account data can have two kinds" when a non-account-data kind (e.g., MessageLike) is combined with any other kind using '+' in the event content derive attribute.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.