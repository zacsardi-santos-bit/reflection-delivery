## Description

When the benchmarking comparison tool generates its markdown reports, the per-harness benchmark data tables are always fully expanded inline. For reports that compare many benchmarks across multiple metrics, this makes the output hard to scan — all raw data tables are visible at once, with no way for readers to collapse sections they don't immediately care about.

## Expected Behavior

- Each metric's benchmark breakdown table should be wrapped in a collapsible section
- The collapsible section should have a visible label indicating it shows a "Breakdown by harness"
- The section should be collapsed by default, allowing readers to expand only the metrics they're interested in
- This collapsible wrapping should apply to every metric section in the output (ratio metrics, success metrics, etc.)

## Why This Matters

Reports comparing benchmarks across many metrics can be very long and difficult to read when all tables are always expanded. Wrapping each table in a collapsible section allows developers to see a high-level summary first and drill into specific metric breakdowns on demand, without losing any of the existing data.
