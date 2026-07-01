I'm working on the statsd protocol parser and need to make a few improvements. Right now, the parser completely ignores the metric type field that's part of every statsd line — whether something is a counter, gauge, histogram, or other type just gets thrown away during parsing. This is a problem because once the data is ingested, there's no way to know what type of aggregation should be applied to each metric.

I'd like the parser to capture the metric type and attach it as a special label on each parsed metric, so that downstream processing can distinguish between different metric types. This label should be the first label on each metric, appearing before any user-defined tags.

I also want to enforce that the metric type field is required. Currently the parser accepts lines that don't have a type at all — I want those to be rejected as invalid.

Additionally, some statsd clients support packing multiple numeric values into a single line using colons to separate them. The parser should handle those packed values by capturing all of them rather than stopping at the first one. If any of the packed values isn't a valid number, the whole line should fail to parse cleanly. Lines that include extra fields like container identifiers or timestamp suffixes should have those fields silently ignored.
