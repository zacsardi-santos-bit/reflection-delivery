I'm working with a time-series database that supports PromQL queries, and I've run into a couple of issues I'd like to address.

First, the method for running PromQL queries against the database doesn't accept any time range parameters — the start time, end time, step interval, and lookback window are all hardcoded inside the implementation. This means every query always uses the same time range, which isn't useful in practice. I need this method to accept explicit time range parameters from the caller instead of relying on hardcoded defaults.

Second, PromQL aggregation queries — things like summing or averaging metrics grouped by certain labels, or aggregating while excluding certain labels from the grouping key — don't seem to be properly supported or tested. I'd like these to work correctly, including edge cases like empty grouping key lists or having no grouping modifier at all.

Additionally, I noticed that the shared test setup automatically creates a default table for every test, even tests that don't need it. This implicit table creation causes confusion when tests use differently named tables. It would be cleaner if each test created only the tables it actually needs, and the shared setup just returns a bare instance.

Can you help fix the PromQL execution interface to accept explicit time range parameters, ensure PromQL aggregation operations work correctly, and clean up the test setup utility?
