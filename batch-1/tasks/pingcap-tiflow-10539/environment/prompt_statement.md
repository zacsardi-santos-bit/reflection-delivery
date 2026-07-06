I'm working on a change data capture system and I've noticed that DDL events are being incorrectly dropped at the wrong stage of the pipeline. Specifically, DDL operations like adding columns to tables that are within the replication scope are being silently skipped by the DDL puller, even though they should flow through to later stages for proper evaluation.

The root cause seems to be that two different filtering concepts are merged into a single operation. One is a coarse check — "is this DDL type even supported, and does this table belong to the set of tables being replicated?" — and the other is a finer check — "even though this table is being replicated, has the user configured this specific event to be excluded from downstream delivery based on timestamps, SQL patterns, or event type rules?" Because these are handled together, the puller is making skip decisions based on timestamp values and event-type rules that should only be evaluated at a higher level in the pipeline where the full event context is available.

I'd like to separate these two concerns:
- The coarse check should only consider the DDL type (is it in the allowed set?) and the table/schema name (does it match the replication rules?). It should return a simple boolean with no error.
- The finer check should accept the complete DDL event object and evaluate whether it should be excluded from downstream delivery based on configured timestamps, SQL query patterns, and event type filters. It should return a boolean and an error.

The DDL manager should receive the filter so it can apply the finer check when deciding whether to send DDL events downstream. The DDL puller should only apply the coarse check and no longer skip events based on timestamp values.

Additionally, when specific DDL event types such as table truncation or schema alteration are filtered out by the configured event rules, the affected tables should still be correctly replicated in the downstream — data insertions and other operations on those tables must continue to sync as expected.
