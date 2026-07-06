I'm working with the streaming API for graph runs and I've noticed that when I merge multiple output channels into a single sequence, the events don't come out in the order they were actually produced.

*   StreamChannel._items must store (stamp, item) tuples instead of raw items. The stamp is an integer assigned at push time. When a channel is iterated normally (via iter()), stamps must be stripped so only the raw items are yielded.

*   When a StreamChannel belongs to a StreamMux, each call to push() must assign a stamp from the mux's shared monotonic counter. Stamps must be unique and strictly increasing across all channels in the mux, including the mux's internal events channel (_events).

*   When a StreamChannel is bound standalone (not connected to any StreamMux), all pushed items must receive stamp 0. The _items deque will contain tuples like (0, item).

*   GraphRunStream.interleave(*names) must yield (name, item) tuples in strict arrival order determined by each item's push stamp, not in round-robin rotation across channels. The item with the lowest stamp across all named channels must be yielded first.

*   If any named channel passed to interleave() already has a subscriber, interleave() must raise RuntimeError with a message containing the substring 'already has a subscriber'.

*   After interleave() finishes (normally, via early close, or after an error), it must release all channel subscriptions by setting each channel's _subscribed attribute to False.

*   If subscription validation fails on a later channel during interleave() setup, any channels that were already subscribed must still be released (their _subscribed set to False).

*   If interleave() is called with a channel name that does not exist in the extensions mapping, it must raise KeyError or AttributeError.

*   Errors signaled on a channel (via its fail mechanism) must propagate through the interleave() generator to the caller.

*   Channels with no items must produce no (name, item) tuples in the interleave() output; they are silently skipped.


*   Interface details: Type: Class
Name: StreamChannel
Location: libs/langgraph/langgraph/stream/stream_channel.py
Description: A typed channel that buffers pushed items for iteration. Internal _items deque now stores (stamp, item) tuples. When iterated normally, stamps are stripped and only raw items are yielded.
Signature:
  __init__(name: str | None = None, maxlen: int | None = None)
  _bind(*, is_async: bool) -> None
  _bind_mux(mux: StreamMux) -> None
  push(item: T) -> None  # stores (stamp, item) in _items; stamp=0 if no mux
  close() -> None
  fail(error: BaseException) -> None
  _items: deque[tuple[int, T]]   # (stamp, item) pairs; stamp is 0 for standalone channels
  _subscribed: bool
  _closed: bool
  _error: BaseException | None
  _is_async: bool | None

Type: Class
Name: StreamMux
Location: libs/langgraph/langgraph/stream/_mux.py
Description: Multiplexer that owns a shared monotonic push-stamp counter and routes events to registered transformers and channels.
Signature:
  _next_push_seq() -> int   # increments and returns _push_seq; used by StreamChannel.push()
  _push_seq: int            # shared monotonic counter for push stamps
  _events: StreamChannel[ProtocolEvent]
  extensions: dict[str, Any]
  bind_pump(fn: Callable[[], bool]) -> None
  close() -> None

Type: Class
Name: GraphRunStream
Location: libs/langgraph/langgraph/stream/run_stream.py
Description: Handle for a synchronous graph run, providing iteration over named output projections.
Signature:
  interleave(*names: str) -> Iterator[tuple[str, Any]]
  extensions: dict[str, Any]


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.