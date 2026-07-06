I'm using the Vercel Workers Python SDK to enqueue jobs, and I keep running into serialization errors when my payload includes common Python types like unique identifiers, timestamps, or decimal numbers.

*   A new class named WorkerJSONEncoder must be defined in python/vercel-workers/src/vercel/workers/client.py and must be importable as vercel.workers.client.WorkerJSONEncoder. It must subclass json.JSONEncoder.

*   WorkerJSONEncoder must override the default method so that: UUID values are serialized as strings (str(o)), datetime and date values are serialized as their ISO format string (o.isoformat()), and Decimal values are serialized as floats (float(o)).

*   WorkerJSONEncoder must raise TypeError (via the parent class default method) for any type it does not explicitly handle.

*   The send() function in python/vercel-workers/src/vercel/workers/client.py must accept an optional keyword parameter json_encoder of type type[json.JSONEncoder] | None with a default value of None.

*   When json_encoder is None (the default), send() must use WorkerJSONEncoder to serialize the payload, so UUID, datetime, and Decimal values in the payload are automatically converted to their JSON-compatible representations.

*   When json_encoder is provided as a non-None custom encoder class, send() must use that encoder class to serialize the payload instead of WorkerJSONEncoder.

*   The Django backend's enqueue method must serialize task positional arguments containing UUID values as strings in the serialized JSON body's args list.

*   The Django backend's enqueue method must serialize task positional arguments containing datetime values as ISO format strings in the serialized JSON body's args list.

*   The Django backend's enqueue method must serialize task keyword arguments containing Decimal values as float values in the serialized JSON body's kwargs dict.


*   Interface details: Type: Class
Name: WorkerJSONEncoder
Location: python/vercel-workers/src/vercel/workers/client.py
Description: A custom JSON encoder that handles common Python types not supported by the standard library JSON encoder. Must subclass json.JSONEncoder. The default(o) method must handle: UUID → str(o), datetime and date → o.isoformat(), Decimal → float(o), and must call super().default(o) for all other types (which raises TypeError).
Signature: default(self, o: Any) -> Any

Type: Function
Name: send
Location: python/vercel-workers/src/vercel/workers/client.py
Description: Sends a message to a named queue. Must accept an optional json_encoder keyword parameter. When json_encoder is None (default), WorkerJSONEncoder is used to serialize the payload. When a custom encoder class is supplied, that class is used instead.
Signature: send(queue: str, payload: Any, content_type: str = "application/json", timeout: float | None = 10.0, headers: dict[str, str] | None = None, json_encoder: type[json.JSONEncoder] | None = None) -> SendMessageResult


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.