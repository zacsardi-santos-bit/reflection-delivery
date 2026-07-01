Consolidate the paginator infrastructure in the Matrix Rust SDK by organizing all paginator types into a single module and introducing a new trait for thread pagination. Update the room data provider abstraction to utilize this new trait and simplify error handling for pinned events.

*   Create a new public module named `paginators` in the `matrix-sdk` crate:
    *   File path: `crates/matrix-sdk/src/paginators/mod.rs`
    *   Declare as `pub mod paginators` in `crates/matrix-sdk/src/lib.rs`
    *   Ensure it is accessible as `matrix_sdk::paginators`
*   Move and re-export the following types from `matrix_sdk::event_cache::paginator` to `matrix_sdk::paginators`:
    *   `PaginableRoom` trait
    *   `Paginator` struct
    *   `PaginatorError` enum
    *   `PaginationResult` struct
*   Create a sub-module `thread` within the `paginators` module:
    *   File path: `crates/matrix-sdk/src/paginators/thread.rs`
    *   Ensure it is accessible as `matrix_sdk::paginators::thread`
*   Define a new trait `PaginableThread` in `matrix_sdk::paginators::thread`:
    *   Requires `SendOutsideWasm` and `SyncOutsideWasm` supertraits
    *   Include two async methods:
        *   `relations(thread_root: OwnedEventId, opts: RelationsOptions) -> Result<Relations, matrix_sdk::Error>`
        *   `load_event(event_id: &OwnedEventId) -> Result<TimelineEvent, matrix_sdk::Error>`
*   Move `ThreadedEventsLoader` struct from `matrix-sdk-ui`'s timeline module to `matrix_sdk::paginators::thread`:
    *   Make it generic over `PaginableThread`
*   Update the `RoomDataProvider` trait in `crates/matrix-sdk-ui/src/timeline/traits.rs`:
    *   Include `PaginableThread` as a supertrait bound
    *   Remove the `relations` method (now provided via `PaginableThread`)
*   Modify the `PinnedEventsRoom` trait's `load_event_with_relations` method:
    *   Change the return type to `BoxFuture<'a, Result<(TimelineEvent, Vec<TimelineEvent>), matrix_sdk::Error>>`
*   Ensure the `Room` type implements the `PaginableThread` trait:
    *   Implement `relations` by delegating to the room's relations method
    *   Implement `load_event` by delegating to the room's event fetching method

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.