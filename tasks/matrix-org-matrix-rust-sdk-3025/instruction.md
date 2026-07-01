Implement explicit timeline initialization and a declarative event-type filter for the Matrix SDK's room list service. Separate timeline initialization from access, allowing configuration before creation, and introduce a filter type to simplify event-type filtering.

*   Implement a new event-type filter:
    *   Create `TimelineEventTypeFilter` enum in `crates/matrix-sdk-ui/src/timeline/event_type_filter.rs`.
        *   Variants: `Include(Vec<TimelineEventType>)` and `Exclude(Vec<TimelineEventType>)`.
    *   Re-export `TimelineEventTypeFilter` from `crate::timeline`.
    *   Implement `pub fn filter(&self, event: &AnySyncTimelineEvent) -> bool`:
        *   For `Include`, return `true` if the event type is in the list.
        *   For `Exclude`, return `true` if the event type is NOT in the list.
    *   Ensure the filter method can be used in an event_filter closure in `TimelineInnerSettings`.

*   Update the Room type in `crates/matrix-sdk-ui/src/room_list_service/room.rs`:
    *   Change `timeline` method to `pub fn timeline(&self) -> Option<Arc<Timeline>>`.
        *   Return `None` if the timeline is not initialized.
    *   Add `pub async fn init_timeline_with_builder(&self, builder: TimelineBuilder) -> Result<(), Error>`.
        *   Return `Err(Error::TimelineAlreadyExists(room_id))` if already initialized.
    *   Add `pub fn default_room_timeline_builder(&self) -> TimelineBuilder`.
        *   Return a pre-configured default timeline builder.

*   Add a new error variant:
    *   `Error::TimelineAlreadyExists(OwnedRoomId)` in `crates/matrix-sdk-ui/src/room_list_service/mod.rs`.
    *   Display message: "A timeline instance already exists for room {room_id}".

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.