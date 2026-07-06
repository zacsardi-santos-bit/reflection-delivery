I'm working on the session event pipeline in our editor extension and I've found two related problems I need to fix.

*   When translateSpan processes a span whose resulting event (e.g., a user.message) would exceed the maximum allowed event size (~100KB), that event must be completely dropped from the returned array — not truncated. The function must still return all other events from the same span that fit within the limit.

*   When translateSpan drops an oversized event, it must increment state.droppedCount by one for each dropped event.

*   When translateSpan drops an event, only kept events must advance the internal lastEventId tracker. This ensures that subsequent kept events have their parentId pointing to the last kept event's id, preserving a valid parentId chain across the gap left by the dropped event.

*   When translateDebugLogEntry processes a log entry whose resulting event exceeds the size threshold, the function must return an empty array for that call and increment state.droppedCount by one.

*   When translateDebugLogEntry drops an event, subsequent entries must produce events whose parentId references the last kept event's id, not the dropped entry's id. The parentId chain must remain unbroken across dropped entries.

*   MAX_USER_MESSAGE_LENGTH must be exported as a named constant from extensions/copilot/src/extension/chronicle/common/sessionStoreTracking.ts. The session reindexer must truncate stored user_message values to at most MAX_USER_MESSAGE_LENGTH characters.

*   MAX_ASSISTANT_RESPONSE_LENGTH must be exported as a named constant from extensions/copilot/src/extension/chronicle/common/sessionStoreTracking.ts. The session reindexer must truncate stored assistant_response values to at most MAX_ASSISTANT_RESPONSE_LENGTH characters.


*   Interface details: Type: Constant
Name: MAX_USER_MESSAGE_LENGTH
Location: extensions/copilot/src/extension/chronicle/common/sessionStoreTracking.ts
Description: Exported named constant defining the maximum character count allowed for stored user messages in the session store. Must be exported so the session reindexer and tests can import it by name and use it as the truncation bound.

Type: Constant
Name: MAX_ASSISTANT_RESPONSE_LENGTH
Location: extensions/copilot/src/extension/chronicle/common/sessionStoreTracking.ts
Description: Exported named constant defining the maximum character count allowed for stored assistant responses in the session store. Must be exported so the session reindexer and tests can import it by name and use it as the truncation bound.

Type: Function
Name: translateSpan
Location: extensions/copilot/src/extension/chronicle/common/eventTranslator.ts
Description: Translates a completed span into an array of session events. When a candidate event's estimated serialized JSON size exceeds MAX_EVENT_SIZE, the event must be dropped (excluded from the returned array) rather than having its content truncated. Dropping an event must increment state.droppedCount. Only kept events may advance state.lastEventId, ensuring that subsequent events' parentId chains skip over dropped events and link to the last kept event's id.

Type: Function
Name: translateDebugLogEntry
Location: extensions/copilot/src/extension/chronicle/common/eventTranslator.ts
Description: Translates a single debug log entry into an array of session events. When the resulting event would exceed MAX_EVENT_SIZE, the function must return an empty array for that call and increment state.droppedCount. The parentId chain for subsequent entries must reference the last kept event's id, not the dropped event.

Type: Property
Name: droppedCount
Location: SessionTranslationState (extensions/copilot/src/extension/chronicle/common/eventTranslator.ts)
Description: Numeric counter on the session translation state object that tracks the total number of events dropped due to exceeding the size threshold. Must be accessible as state.droppedCount and must be incremented once per dropped event.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.