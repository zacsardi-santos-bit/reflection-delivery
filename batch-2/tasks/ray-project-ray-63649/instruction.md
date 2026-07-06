I'm seeing a slot leak in the singleton thread router when requests are cancelled at just the wrong moment.

*   When an outer async task is cancelled after the replica-selection context manager's entry phase has already completed on the router's internal loop (reserving a slot), the bridge mechanism must explicitly call the context manager's exit phase to release the slot — it must not rely on garbage collection.

*   After such a cancellation, the reserved-slot count must return to zero; no slots may remain held by the cancelled request.

*   The release must happen via explicit exit (not GC/finalizer): specifically, the exit path must not be triggered by a GeneratorExit exception.

*   The slot release must complete within a reasonable timeout (2 seconds) after the outer task is cancelled.

*   Cancellation of the outer task must still propagate correctly to the caller — the outer task must raise asyncio.CancelledError.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.