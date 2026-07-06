## Description

Two improvements are needed to the streaming pipeline framework:

**1. Windowers need vertex context**

The fixed, session, and sliding windowing strategies are currently constructed with only timing parameters (duration, gap, or length+slide). There is no way for a windower to know which vertex instance or pipeline it belongs to. We need to pass this context at construction time so the windowers can associate themselves with the right vertex and pipeline — enabling per-vertex window observability metrics.

**2. Tick generator needs jitter support**

The built-in tick-based message generator produces messages at a fixed rate with deterministic event times. For testing out-of-order message handling in reduce pipelines, it would be valuable to optionally introduce random delays into the generated messages' event times. For example, a configured jitter of 10 seconds would cause each message's event time to be randomly delayed by 0 to 10 seconds relative to the actual generation time, resulting in messages appearing out of order by up to 10 seconds.

## Expected Behavior

- All three windower constructors should accept a vertex instance parameter in addition to their existing timing parameters.
- The tick generator configuration should support an optional jitter field (a duration).
- When jitter is configured and positive, generated message event times should be randomly shifted back by up to the jitter amount.
- When jitter is not set or is zero, generator behavior is unchanged.
- The internal utility that converts nanosecond timestamps to time values must be updated to also accept a jitter parameter.

## Why This Matters

Window count metrics tied to specific vertices and pipelines are essential for operational observability. The jitter feature enables realistic simulation of out-of-order message scenarios without needing an external data source, improving testing of reduce and windowing pipelines.
