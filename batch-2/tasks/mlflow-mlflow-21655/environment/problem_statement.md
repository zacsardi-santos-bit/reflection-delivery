I'm working on the GenAI issue discovery pipeline and there's a handful of gaps I keep hitting when running discovery over a batch of traces. First thing: the AI model comes back with whatever category labels it wants, and I want to clamp those to a fixed taxonomy. So I want to pass a list of valid categories into the discovery function and have it flow through to clustering and summarization, and anything the model returns that isn't in my list just gets silently dropped so only recognized categories show up on discovered issues.

Next, I've got zero visibility into spend. The discovery result object needs a cost field that reflects the accumulated USD across all the LLM calls, and it should be 0.0 when no traces were processed but a positive number once traces actually get analyzed. Related to that, I want a token counter utility that accumulates input tokens, output tokens, and cost off the LLM responses.

Also right now discovery always spins up a brand new MLflow run. I want to be able to hand it an existing run ID and have it attach there instead, with the result's triage run ID matching what I passed and the run staying accessible afterward. And when the pipeline does create its own run, it should tag it so it's identifiable as an issue detection run, distinct from general evaluation run types.

Oh and the summary text starts with a markdown heading which makes it awkward to embed in other UI. Drop the heading prefix so it's plain text, and for the zero-issues case it should just read like "Analyzed N traces. No issues found." with no heading.

Last thing, I need a helper that groups traces by session identifier, where traces without a session each land in their own individual group, and all traces within a group come back sorted by timestamp.
