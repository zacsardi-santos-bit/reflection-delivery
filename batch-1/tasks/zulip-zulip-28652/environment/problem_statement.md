## Description

The markdown rendering module currently exposes two functions that work by mutating a message object passed as an argument. One function parses and renders raw message content, writing the rendered HTML and message flags back onto the passed object. Another function extracts topic links from the message, again writing the results back onto the object. Additionally, the topic-link extraction function requires callers to pass in the linkifier configuration at the call site, rather than reading it from the module's own stored state.

This mutation-based design makes the module harder to use correctly: callers must construct a throwaway object just to retrieve values, and the side-effectful API is surprising to new contributors. The topic-link function's requirement to pass the linkifier map at every call site is also redundant, since the module already maintains its own initialized state.

## Expected Behavior

- The content rendering function should accept a raw content string and return a new object containing the rendered HTML, message flags, and a status-message indicator — without mutating any input object.
- The topic-link extraction function should accept a topic string and return an array of link objects — without mutating any input object and without requiring the caller to pass a linkifier configuration.
- The topic-link extraction should use the linkifier rules already stored by the module's initialization function.
- When a topic is absent (e.g., for non-stream messages), the function should return an empty array.

## Why This Matters

Adopting a pure, value-returning API for these functions makes the module's contract clearer, reduces surprise from hidden side effects, and simplifies callers that previously had to construct and then inspect mutated objects.
