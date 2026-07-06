I'm working on restructuring the paginator infrastructure in the Matrix Rust SDK. Right now, the paginator types used for room-level event pagination are buried inside the event cache module, and the logic for paginating through threaded conversations lives inside the UI timeline module. This makes everything hard to find and reuse.

I'd like to consolidate all the paginator types into a single, dedicated top-level module in the core SDK crate. As part of this, I want to introduce a new trait specifically for thread pagination — one that has methods for fetching thread relations and loading individual events. The thread events loader should be moved to this new location and made generic over that new trait.

On the data provider side, the generic room data provider abstraction currently has thread-relations functionality baked directly into it. That should be removed and instead the data provider should require the new thread pagination trait as a supertrait bound. This means types implementing the data provider interface only need to implement the thread-specific trait separately, which makes testing much cleaner.

Additionally, the error type used for loading pinned events with relations should be simplified to use the standard SDK error type rather than a paginator-specific error wrapper, since the paginator error was just wrapping the SDK error anyway.

The test infrastructure for the timeline module needs to be updated to reflect these changes — the mock room data provider used in tests should implement the new thread pagination trait, and the relations method should be removed from the general room data provider implementation since it is now provided via the new supertrait.
