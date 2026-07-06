## Description

The coordinator duty responsible for killing unused segments does not distribute work fairly across datasources. When multiple datasources have unused segments to clean up, the duty may repeatedly pick the same datasource in consecutive runs, while other datasources continue to accumulate unused segments. There is also a bug where datasources that initially have no unused segments are not processed correctly in later runs when unused segments appear.

## Expected Behavior

- When multiple datasources have unused segments and kill task slots are available, the coordinator should cycle through datasources in a round-robin fashion across runs, avoiding selecting the same datasource in back-to-back runs as long as other datasources are eligible.
- When the set of datasources eligible for cleanup changes between runs, the scheduler should adapt to the new set while still respecting the round-robin ordering.
- When only one datasource is eligible for cleanup, it should be selected in every run as expected.
- When a datasource initially has no unused segments (e.g., only used segments exist), the duty should report zero eligible segments for that run and then correctly process it in subsequent runs when unused segments appear.

## Why This Matters

Without fair scheduling, some datasources may accumulate a large backlog of unused segments while others are cleaned up repeatedly. This creates imbalanced resource usage and delayed reclamation of storage. The fix ensures that kill task slots are distributed equitably across all datasources that need cleanup over time.
