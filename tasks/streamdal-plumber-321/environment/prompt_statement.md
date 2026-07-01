I'm working on improving the replay listing output in our batch backend. Right now, when replays are listed, the source information isn't very descriptive — it just shows a raw collection name without any label indicating what kind of source it is. There's also no support for showing dead-letter stage sources at all, so if a replay is sourced from a stage rather than a collection, that information is completely missing from the output.

I'd like the source column to show a clearly labeled string based on the source type: something like "Collection - <name>" for collection-based replays, and "Dead Letter Stage - <name>" for stage-based replays. The replay type field should also be normalized to be more human-readable (title-cased) instead of showing the raw lowercase value from the API.

Additionally, the schema listing currently shows a field for the protobuf root type, which is never actually useful in the output. I'd like that field removed from the schema output struct as well.
