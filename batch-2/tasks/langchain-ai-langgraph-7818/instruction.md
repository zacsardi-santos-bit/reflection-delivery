I'm working on the Python SDK for LangGraph and need to implement the streaming layer for the new event protocol.

*   MultiCursorBuffer must be a generic async iterable class. When iterated, it replays all items buffered so far from index zero, then waits for new items to be pushed. Every independent iterator over the same buffer must receive the full sequence of items from the beginning.

*   MultiCursorBuffer.push(item) must append an item to the internal buffer and wake up any iterators currently waiting for new items.

*   MultiCursorBuffer.close() must signal that no further items will be pushed. Any iterators currently waiting for new items must be released and terminate cleanly (yielding no further items).

*   MultiCursorBuffer must support len(), returning the current count of buffered items.

*   MultiCursorBuffer must expose a _wakeups attribute whose len() equals the number of iterators currently suspended waiting for new items.

*   normalize_segment(segment) must return the segment string with any suffix starting at the first colon removed. If no colon is present the string is returned unchanged.

*   is_prefix_match(namespace, prefix) must return True if namespace starts with all segments in prefix, where each namespace segment is first normalized (colon-suffix stripped) before comparison. If prefix itself contains a colon it is treated as an exact literal. An empty prefix matches any namespace. Returns False if prefix is longer than namespace.

*   namespace_matches(namespace, prefixes, depth) must return True when prefixes is None or an empty list, regardless of namespace. When a depth limit is given, namespaces deeper than the limit must not match even if a prefix matches. A namespace matches when at least one prefix satisfies is_prefix_match and the depth constraint.

*   infer_channel(event) must map event method names to channel names as follows: 'values'->'values', 'checkpoints'->'checkpoints', 'updates'->'updates', 'messages'->'messages', 'tools'->'tools', 'lifecycle'->'lifecycle', 'tasks'->'tasks', 'input.requested'->'input'. For method 'custom', if the event data contains a non-empty 'name' field the channel is 'custom:{name}'; if the name is absent or empty the channel is 'custom'. For any other method the function returns None.

*   matches_subscription(event, subscription) must return True only when the inferred channel for the event matches one of the channels listed in the subscription dict. A bare 'custom' entry in channels must match any namespaced custom channel (e.g. 'custom:my_ext'). When the subscription includes a 'namespaces' filter, namespace_matches must also pass; events whose namespace does not satisfy the filter must return False.

*   EventStreamHandle must accept constructor keyword arguments: events (an async iterator of event dicts), ready (a Future that resolves when the stream is open), done (a Future that resolves when the stream ends), and close (an async callable). It must expose .ready and .done as properties returning those futures, .events as the async iterator, and an async .close() method that invokes the provided close callable.

*   ProtocolSseTransport must accept constructor arguments client (httpx.AsyncClient), thread_id (str), and an optional max_queue_size (int, default 1024). The value must be stored as the instance attribute _max_queue_size.

*   ProtocolSseTransport.send_command(command) must POST the command dict as JSON to /threads/{thread_id}/commands and return the response body parsed as a dict on HTTP 200. On HTTP 202 it must return None. On 4xx responses it must raise httpx.HTTPStatusError. On a 200 response with an empty body it must raise RuntimeError with a message matching 'did not return a valid response'. After the transport has been closed, calling send_command must raise RuntimeError with a message matching 'closed'.

*   ProtocolSseTransport.open_event_stream(subscription) must POST to /threads/{thread_id}/stream/events, passing the subscription dict (including any 'since' and 'channels' fields) as the request body, and return an EventStreamHandle. The handle's ready future must resolve once the SSE connection is established. The handle's events async iterator must yield parsed event dicts. The queue used for backpressure must be bounded by max_queue_size so a slow consumer suspends the pump rather than buffering unboundedly. Calling handle.close() must cancel in-flight iteration so that subsequent __anext__() calls raise StopAsyncIteration without hanging.

*   When the SSE response body raises an exception after the ready future has resolved, the exception must be set on the handle's done future (awaiting done returns the exception instance). When the stream ends cleanly, done must resolve to None. After close() is called, the decoder must not emit any additional events from a final flush.


*   Interface details: Type: Class
Name: MultiCursorBuffer
Location: libs/sdk-py/langgraph_sdk/stream/multi_cursor_buffer.py
Description: A generic async buffer that supports multiple independent iterators, each receiving all items from index zero. Items pushed after an iterator starts waiting are delivered to it. Closing the buffer releases all waiting iterators.
Signature:
  push(item: T) -> None
  close() -> None
  __len__() -> int
  __aiter__() -> AsyncIterator[T]
  _wakeups: collection where len(_wakeups) equals the number of currently-suspended iterators


Type: Function
Name: normalize_segment
Location: libs/sdk-py/langgraph_sdk/stream/subscription.py
Signature: normalize_segment(segment: str) -> str
Description: Strips the suffix starting at the first colon from a segment string. Returns the string unchanged if no colon is present.


Type: Function
Name: is_prefix_match
Location: libs/sdk-py/langgraph_sdk/stream/subscription.py
Signature: is_prefix_match(namespace: list[str], prefix: list[str]) -> bool
Description: Returns True if namespace starts with all segments in prefix. Each namespace item is normalized (colon-suffix stripped) before comparison; prefix items are treated as exact literals (colons in a prefix item mean exact match). An empty prefix matches any namespace. Returns False if prefix is longer than namespace.


Type: Function
Name: namespace_matches
Location: libs/sdk-py/langgraph_sdk/stream/subscription.py
Signature: namespace_matches(namespace: list[str], prefixes: list[list[str]] | None, depth: int | None) -> bool
Description: Returns True when prefixes is None or empty (matches anything). When depth is provided, namespaces longer than depth do not match. A namespace matches when at least one prefix passes is_prefix_match and the depth constraint is satisfied.


Type: Function
Name: infer_channel
Location: libs/sdk-py/langgraph_sdk/stream/subscription.py
Signature: infer_channel(event: dict) -> str | None
Description: Maps the event's 'method' field to a channel name. Method-to-channel mapping: 'values'->'values', 'checkpoints'->'checkpoints', 'updates'->'updates', 'messages'->'messages', 'tools'->'tools', 'lifecycle'->'lifecycle', 'tasks'->'tasks', 'input.requested'->'input'. For method 'custom': returns 'custom:{name}' if the event data has a non-empty 'name' field, otherwise 'custom'. Returns None for unknown methods.


Type: Function
Name: matches_subscription
Location: libs/sdk-py/langgraph_sdk/stream/subscription.py
Signature: matches_subscription(event: dict, subscription: dict) -> bool
Description: Returns True if the event's inferred channel is in subscription['channels'] and, if subscription has a 'namespaces' key, the event's namespace passes namespace_matches. A bare 'custom' entry in channels matches any 'custom:*' channel.


Type: Class
Name: EventStreamHandle
Location: libs/sdk-py/langgraph_sdk/stream/transport/http.py
Description: A handle to an open SSE event stream. Constructed with async iterator of events, ready Future, done Future, and async close callable. Exposes .ready, .done, .events, and async .close().
Signature:
  __init__(self, *, events: AsyncIterator, ready: asyncio.Future, done: asyncio.Future, close: Callable) -> None
  ready: asyncio.Future  (property)
  done: asyncio.Future   (property)
  events: AsyncIterator  (property or attribute)
  async close() -> None


Type: Class
Name: ProtocolSseTransport
Location: libs/sdk-py/langgraph_sdk/stream/transport/http.py
Description: HTTP transport for the LangGraph streaming protocol. Sends commands and opens SSE event streams against a specific thread.
Signature:
  __init__(self, *, client: httpx.AsyncClient, thread_id: str, max_queue_size: int = 1024) -> None
  _max_queue_size: int
  async send_command(command: dict) -> dict | None
    - POSTs to /threads/{thread_id}/commands as JSON
    - Returns parsed JSON dict on HTTP 200
    - Returns None on HTTP 202
    - Raises httpx.HTTPStatusError on 4xx responses
    - Raises RuntimeError (message matches "did not return a valid response") on 200 with empty body
    - Raises RuntimeError (message matches "closed") if called after close()
  open_event_stream(subscription: dict) -> EventStreamHandle
    - POSTs to /threads/{thread_id}/stream/events with subscription dict as body
    - Passes 'since' and 'channels' fields from subscription in the request body
    - Returns EventStreamHandle whose ready resolves when SSE connection is established
    - Uses a bounded async queue of size max_queue_size for backpressure
    - Closing the handle stops iteration; subsequent __anext__() raises StopAsyncIteration
    - Mid-stream errors set on done future; clean end resolves done to None
    - After close(), decoder flush must not emit additional events
  async close() -> None


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.