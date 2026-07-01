Implement an adapter for the Scala MongoDB driver to handle operations that complete without returning meaningful values, such as file uploads to GridFS. Ensure these operations emit a single Scala unit value upon successful completion and propagate errors appropriately. Update the GridFS upload observable to use this new unit-emitting type.

Requirements:

*   Implement `UnitObservable`:
    *   Accept any `Observable` as a constructor argument.
    *   Extend `SingleObservable[Unit]`.
    *   Emit exactly one `Unit` value when the underlying observable completes successfully, regardless of the number of elements emitted by the underlying observable, and then signal completion.
    *   Propagate errors from the underlying observable: emit zero items, call `onError` with the original error, and do not call `onComplete`.
    *   Support explicit backpressure demand with `request(1)`, emitting one `Unit` item and completing.
    *   Support Scala for-comprehension chaining using `flatMap` and `map`.

*   Implement `ToGridFSUploadPublisherUnit`:
    *   Accept a `GridFSUploadPublisher[Void]` and implement `GridFSUploadPublisher[Unit]`.
    *   Emit exactly one `Unit` value when the wrapped publisher completes without error, even if it emits no `Void` items, and signal completion.
    *   Propagate errors from the wrapped publisher: emit zero items, call `onError` with the original error, and do not call `onComplete`.
    *   Support explicit backpressure demand with `request(1)`, emitting one `Unit` item and completing.
    *   Delegate `getObjectId` and `getId` to the wrapped `GridFSUploadPublisher[Void]`.

*   Update `GridFSUploadObservable`:
    *   Ensure it is usable with a `Unit` type parameter.
    *   Ensure `GridFSUploadObservable[Unit]` exposes the same set of method names as `GridFSUploadPublisher[Unit]`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.