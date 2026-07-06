I'm working with a distributed task queue system in Go and I need two utility functions to handle passing structured data through the queue. Right now there's no standard way to serialize a Go value into the task argument format the queue library expects, or to deserialize the result back into a typed struct once a task completes.

What I need is a way to take any Go value, serialize it into a single task argument using a standard encoding format, and then later take the raw reflected result values from the queue and deserialize the first one back into the original type. The round trip should be lossless — the deserialized value should be equal to what was originally serialized. The deserialization function should also return an error if the result contains more than one element, since only a single encoded string is expected.

Both functions should live in the existing tasks package alongside the other task queue setup code.
