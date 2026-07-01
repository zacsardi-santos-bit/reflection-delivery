## Description

The SDK currently has paginator types scattered across different internal modules. The room-level paginator infrastructure lives inside the event cache module, while the code for paginating through threaded conversations lives inside the UI timeline module. This fragmentation makes the paginator types hard to discover, reuse, and maintain.

Additionally, the generic room data provider abstraction currently includes a thread-relations method directly on it, which conflates two separate concerns and creates inflexibility when different types need to be used for thread pagination versus general room data access.

## Expected Behavior

- All paginator types should be consolidated into a single, dedicated top-level module in the core SDK crate, making them easy to find and import.
- A new trait should be introduced specifically for thread-level pagination, separating the concern of "how to paginate a thread" from the general room data provider interface.
- The room data provider abstraction should require the new thread pagination trait as a supertrait rather than having thread-relations functionality inline.
- The error type for loading pinned events with relations should use the standard SDK error type rather than a paginator-specific error wrapper.
- The thread events loader should be moved to the core SDK crate alongside other paginator types, and should be generic over the new thread pagination trait.

## Why This Matters

This reorganization allows paginator infrastructure to be reused across different parts of the SDK without importing from internal or UI-specific modules. It also makes the trait boundaries clearer and more composable, which is important for testing — implementations for tests only need to satisfy the relevant interface rather than a monolithic data provider trait.
