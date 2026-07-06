I've been digging into how we record tool interaction telemetry when the agent accepts file edits, and there's a few things that are off and messing up the data quality.

First, the accepted line count is wrong. Right now it only counts lines that were added, but I want it to sum both added and removed lines together so the number actually reflects the full scope of the change, not just half of it.

Second, when a file gets edited we're not recording the programming language at all, even though it's sitting right there and can be derived from the file extension. I want the detected language included in the telemetry interaction record so we can see which file types the AI is editing most often.

Third, and this one's the noisiest problem, every accepted tool call is currently emitting a telemetry interaction event, including tools that have nothing to do with editing files. Those are getting logged as a generic "unknown" interaction which is just clutter. I only want file-editing tool interactions to generate these events. Everything else should be skipped entirely, silently ignored rather than recorded as unknown.

So basically: fix the line count to be added plus removed, attach the language detected from the extension, and stop emitting interaction events for non-editing tools. Accurate telemetry matters here because miscounted lines, missing language info, and spurious unknown interactions all degrade the insights we can pull from this data.
