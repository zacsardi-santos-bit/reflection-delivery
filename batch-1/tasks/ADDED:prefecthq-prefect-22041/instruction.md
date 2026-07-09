I'm working on the Prefect server and need to implement a reliable cleanup queue for worker communication.

*   The settings module must expose four new settings for the worker cleanup queue: PREFECT_SERVER_WORKER_CHANNEL_CLEANUP_QUEUE_STORAGE (string, default 'prefect.server.worker_communication.cleanup_queue.memory'), PREFECT_SERVER_WORKER_CHANNEL_CLEANUP_LEASE_SECONDS (float, default 30.0), PREFECT_SERVER_WORKER_CHANNEL_CLEANUP_MAX_DELIVERY_ATTEMPTS (int), and PREFECT_SERVER_WORKER_CHANNEL_CLEANUP_COMPLETED_IDEMPOTENCY_RETENTION_SECONDS (float). These must be accessible via both the PREFECT_SERVER_WORKER_CHANNEL_* environment variable names and via the structured settings object at settings.server.worker_channel.*.

*   The prefect.client.schemas.worker_channel module must define two constants: CANCELLING_TIMEOUT_TEARDOWN and PENDING_CLAIM_TEARDOWN, usable as 'kind' values when enqueuing cleanup messages.

*   get_worker_cleanup_queue() must return a WorkerCleanupQueue instance loaded from the module specified by PREFECT_SERVER_WORKER_CHANNEL_CLEANUP_QUEUE_STORAGE. When that setting points to the interface package itself ('prefect.server.worker_communication.cleanup_queue'), it must raise ValueError with a message matching 'concrete WorkerCleanupQueue implementation'.

*   WorkerCleanupQueue.enqueue() must be idempotent: calling it a second time with the same idempotency_key and work_pool_id must return an object whose .message_id equals that of the first enqueue call, regardless of the message_id argument passed to the second call.

*   WorkerCleanupQueue.enqueue() must deep-copy the target and data arguments at enqueue time so that subsequent mutations to the caller's dict do not affect what is stored. The stored message's .target and .data must match the values at the time of enqueue.

*   WorkerCleanupQueue.enqueue() must notify waiting dispatchers (increment the wakeup sequence for the work_pool_id) when a new, non-duplicate message is enqueued.

*   WorkerCleanupQueue.reserve() must return a reservation with .message_id, .delivery_count (starting at 1), .lease_expires_at (== current time + PREFECT_SERVER_WORKER_CHANNEL_CLEANUP_LEASE_SECONDS), and .reservation_token (string with length > 32). Only one active reservation per work_pool_id is allowed at a time; a second call while one is active must return None.

*   WorkerCleanupQueue.reserve() must prefer messages whose work_queue_id is in the preferred_work_queue_ids list (if provided), but fall back to any message in the work pool when no preferred-queue message is available.

*   WorkerCleanupQueue.reserve() must implicitly handle expired leases before looking for available messages: expired leases are redelivered (delivery_count incremented) or dead-lettered (if max attempts reached), and dispatchers are woken when any expired lease is redelivered.

*   WorkerCleanupQueue.ack(), release(), and renew() must enforce work-pool scope: if work_pool_id does not match the reservation's pool, they must return an object with .status == 'unauthorized' and .reason == 'work_pool_mismatch', leaving the message intact.

*   WorkerCleanupQueue.ack() must return .status == 'invalid_token' (leaving the message intact) when the reservation_token does not match the current reservation. When the token is valid, it must return .status == 'accepted' and remove the message from the queue while retaining an idempotency tombstone for the PREFECT_SERVER_WORKER_CHANNEL_CLEANUP_COMPLETED_IDEMPOTENCY_RETENTION_SECONDS duration.

*   After the completed idempotency retention period expires, re-enqueuing with the same idempotency_key must create a new message (new message_id) rather than returning the tombstone.

*   WorkerCleanupQueue.release() must return .status == 'accepted' and make the message available again with delivery_count incremented and a new reservation_token. When delivery_count reaches PREFECT_SERVER_WORKER_CHANNEL_CLEANUP_MAX_DELIVERY_ATTEMPTS, it must instead return .status == 'dead_lettered' and .reason == 'max_delivery_attempts_reached', moving the message to the dead-letter store.

*   Dead-lettered messages must be accessible via read_dead_letter(work_pool_id, message_id). The returned object must have .final_delivery_count and .release_reason fields. read_dead_letter() must return None when the given work_pool_id does not match the message's pool.

*   WorkerCleanupQueue.renew() must return .status == 'accepted' and update .lease_expires_at to (current time + PREFECT_SERVER_WORKER_CHANNEL_CLEANUP_LEASE_SECONDS) for a valid, non-expired reservation. If the lease has already expired, it must return .status == 'expired' and increment the wakeup sequence (waking any waiting dispatchers).

*   WorkerCleanupQueue.expire_leases() must expire all overdue leases, redeliver eligible messages (returning them in .redelivered list with .message_id), and dead-letter those exceeding max attempts (returning them in .dead_lettered). It must respect the work_pool_id scope argument (only expire leases for that pool) and the limit argument (process at most that many expired leases per call).

*   WorkerCleanupQueue.read_wakeup_sequence() must return the current integer sequence counter for the given work_pool_id. WorkerCleanupQueue.wait_for_wakeup() must block until the sequence advances past `after`, then return an object with .work_pool_id and .sequence. If asyncio.TimeoutError occurs, it must return None.


*   Interface details: ## Constants

Type: Constant
Name: CANCELLING_TIMEOUT_TEARDOWN
Location: src/prefect/client/schemas/worker_channel.py
Description: Cleanup kind constant used when enqueueing a message for a flow run that has timed out and is being cancelled.

Type: Constant
Name: PENDING_CLAIM_TEARDOWN
Location: src/prefect/client/schemas/worker_channel.py
Description: Cleanup kind constant used when filtering reservations by cleanup kind.

---

## Settings

The following settings must be added to `prefect.settings` (accessible via `PREFECT_SERVER_WORKER_CHANNEL_*` keys and via the structured settings object at `settings.server.worker_channel.*`):

- `PREFECT_SERVER_WORKER_CHANNEL_CLEANUP_QUEUE_STORAGE` — string, default: `"prefect.server.worker_communication.cleanup_queue.memory"`
- `PREFECT_SERVER_WORKER_CHANNEL_CLEANUP_LEASE_SECONDS` — float, default: 30.0
- `PREFECT_SERVER_WORKER_CHANNEL_CLEANUP_MAX_DELIVERY_ATTEMPTS` — int
- `PREFECT_SERVER_WORKER_CHANNEL_CLEANUP_COMPLETED_IDEMPOTENCY_RETENTION_SECONDS` — float

---

## Functions

Type: Function
Name: get_worker_cleanup_queue
Location: src/prefect/server/worker_communication/cleanup_queue/__init__.py
Signature: get_worker_cleanup_queue() -> WorkerCleanupQueue
Description: Factory function that loads and returns a WorkerCleanupQueue instance from the configured backend module (via PREFECT_SERVER_WORKER_CHANNEL_CLEANUP_QUEUE_STORAGE). Raises ValueError with message matching "concrete WorkerCleanupQueue implementation" if the configured module is the interface package itself (i.e., "prefect.server.worker_communication.cleanup_queue").

Type: Function
Name: now
Location: src/prefect/server/worker_communication/cleanup_queue/memory.py
Signature: now(timezone) -> datetime
Description: Module-level function that returns the current datetime. Used for testability — monkeypatched in tests to control time.

---

## Classes

Type: Class
Name: WorkerCleanupQueue
Location: src/prefect/server/worker_communication/cleanup_queue/memory.py
Description: In-memory implementation of the worker cleanup queue. Provides idempotent enqueue, lease-based reservation, acknowledgment, release, renewal, expiry, dead-lettering, and wakeup notification.

Methods:

Signature: clear() -> None
Description: Resets all internal queue state.

Signature: enqueue(message_id: UUID, idempotency_key: str, work_pool_id: UUID, kind, target: dict, data: dict | None = None, work_queue_id: UUID | None = None) -> result
Description: Enqueues a cleanup message. Idempotent: if a message with the same (idempotency_key, work_pool_id) already exists (and is not expired by retention), returns the existing message's metadata. Deeply copies target and data. Notifies waiting dispatchers. Returns an object with .message_id.

Signature: reserve(work_pool_id: UUID, preferred_work_queue_ids: list[UUID] | None = None, cleanup_kinds: list | None = None) -> reservation | None
Description: Reserves the next available message for the given work_pool_id. Returns None if no message is available or if one is already reserved. Prefers messages from preferred_work_queue_ids if provided, falls back to any message in the pool. Handles implicit lease expiry before checking for available messages (and wakes dispatchers when expired leases are redelivered). The reservation object has: .message_id, .delivery_count (starting at 1, incremented per delivery), .lease_expires_at (current time + PREFECT_SERVER_WORKER_CHANNEL_CLEANUP_LEASE_SECONDS), .reservation_token (string with length > 32).

Signature: ack(work_pool_id: UUID, message_id: UUID, reservation_token: str) -> result
Description: Acknowledges successful processing of a reserved message. Returns an object with .status. Status values: "accepted" (message removed from queue and idempotency tombstone retained), "invalid_token" (reservation_token does not match current reservation), "unauthorized" with .reason == "work_pool_mismatch" (work_pool_id does not match).

Signature: release(work_pool_id: UUID, message_id: UUID, reservation_token: str, reason: str) -> result
Description: Releases a reserved message back to the queue for redelivery, or moves it to the dead-letter queue if max_delivery_attempts is reached. Returns an object with .status and .reason. Status values: "accepted" (message is re-queued with incremented delivery_count and new reservation_token), "dead_lettered" with .reason == "max_delivery_attempts_reached" (message moved to DLQ when delivery_count >= PREFECT_SERVER_WORKER_CHANNEL_CLEANUP_MAX_DELIVERY_ATTEMPTS), "unauthorized" with .reason == "work_pool_mismatch" (wrong work pool).

Signature: renew(work_pool_id: UUID, message_id: UUID, reservation_token: str) -> result
Description: Extends the lease of an active reservation. Returns an object with .status and (when accepted) .lease_expires_at. Status values: "accepted" (lease extended, .lease_expires_at == now + lease_seconds), "expired" (lease already expired; wakes dispatchers by incrementing wakeup sequence), "unauthorized" with .reason == "work_pool_mismatch" (wrong work pool).

Signature: read_message(work_pool_id: UUID, message_id: UUID) -> message | None
Description: Returns the stored message for the given message_id if it belongs to the given work_pool_id. Returns None if not found or if work_pool_id does not match. The message object has .target and .data fields.

Signature: read_dead_letter(work_pool_id: UUID, message_id: UUID) -> dead_letter | None
Description: Returns the dead-letter entry for the given message_id if it belongs to the given work_pool_id. Returns None if not found or if work_pool_id does not match. The dead letter object has .final_delivery_count and .release_reason fields.

Signature: read_wakeup_sequence(work_pool_id: UUID) -> int
Description: Returns the current wakeup sequence counter for the given work_pool_id.

Signature: wait_for_wakeup(work_pool_id: UUID, after: int | None = None, timeout: float | None = None) -> wakeup | None
Description: Waits until the wakeup sequence for work_pool_id advances past `after`. Returns a wakeup object with .work_pool_id and .sequence, or None if the wait times out (asyncio.TimeoutError is caught and returns None).

Signature: expire_leases(work_pool_id: UUID | None = None, limit: int | None = None) -> result
Description: Scans for expired leases and either redelivers or dead-letters them. Respects work_pool_id scope (if provided) and limit. Returns an object with .redelivered (list of items with .message_id) and .dead_lettered (list).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.