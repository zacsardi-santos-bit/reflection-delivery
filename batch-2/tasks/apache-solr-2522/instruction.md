Refactor the slot-acquisition API to improve clarity and manageability. Implement a system where a failed slot acquisition returns nothing, and a successful acquisition returns a closeable resource that automatically releases the slot when the block exits.

Requirements:

* Implement `RequestRateLimiter` class:
    * Expose a public inner type `SlotReservation` that implements `java.io.Closeable`.
        * Replace `SlotMetadata` with `SlotReservation` for slot acquisition methods.
        * Return `null` for `SlotReservation` when no slot is acquired.
        * Ensure `close()` on `SlotReservation` releases the slot.
    * Update `handleRequest()` method:
        * Return `RequestRateLimiter.SlotReservation` instead of `SlotMetadata`.
        * Return `null` if no slot is acquired within `slotAcqTimeMillis`.
        * Declare `throws InterruptedException`.
    * Update `allowSlotBorrowing()` method:
        * Return `RequestRateLimiter.SlotReservation` instead of `SlotMetadata`.
        * Return `null` if no borrowed slot is available.
        * Declare `throws InterruptedException`.
    * Implement `isEmpty()` method:
        * Return `true` when zero `SlotReservation` instances are held.

* Implement `RateLimitManager` class:
    * Update `handleRequest(HttpServletRequest request)` method:
        * Return `RequestRateLimiter.SlotReservation` or `null` if no slot is available after the wait.
    * Implement `getRequestRateLimiter(SolrRequest.SolrRequestType type)` method:
        * Return the `RequestRateLimiter` registered for the given request type.

* Ensure slot acquisition behavior:
    * Block for `slotAcqTimeMillis` milliseconds when all slots are occupied before returning `null`.
    * Complete acquisition quickly when a slot is available, avoiding full timeout delay.
    * Never exceed `allowedRequests` for concurrently held `SlotReservation` instances.
    * Never exceed `allowedRequests - guaranteed` for concurrently held borrowed `SlotReservation` instances.
    * Ensure guaranteed slots remain available for native request types even when the borrow quota is fully consumed.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.