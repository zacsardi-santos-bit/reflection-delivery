I'm working with the agent executor in this framework and I need to add support for streaming and async execution. Right now, you can only run an agent and wait for the full result — there's no way to see each step as it happens, and there's no async interface for non-blocking use.

What I'd like is the ability to iterate over agent steps in real time, both synchronously and asynchronously. Each emitted step should clearly indicate whether it's an action the agent is taking, an observation from a tool, or the final answer. I also need a utility that can combine all the individual streaming chunks into one aggregated result.

Additionally, the existing step-by-step iterator API needs a cleanup: there's a confusing parameter developers have to pass to enable async iteration, which should instead be handled automatically. And I'd like to optionally include run-tracking metadata in the final output so I can trace each run by its unique identifier.

The streaming interface, the async run/call methods, the chunk-aggregation utility, and the iterator API improvements described above need to be added to the agent executor.
