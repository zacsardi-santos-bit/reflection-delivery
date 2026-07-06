# Add configurable wait policies for rollup asset partition mappers

## Description

When a downstream DAG depends on partitioned assets via a rollup mapper, there is currently no option to configure when the rollup is considered "satisfied" — it always waits for every single expected partition key to arrive. This is too rigid for real-world scenarios where:

- A pipeline wants to start processing as soon as a minimum number of upstream partitions have arrived (e.g., fire after 5 of 60 expected keys are ready), or
- A pipeline is tolerant of a small number of stragglers and should fire when at most N partitions are still missing (e.g., fire when at most 3 of 60 keys are absent).

Additionally, the system currently has no way to detect or report when a configured threshold is permanently impossible to satisfy (e.g., requiring more keys than the partition window can ever produce), and instead silently blocks the DAG run forever.

## Expected Behavior

- Users can attach a configurable wait policy to a rollup mapper when defining a DAG schedule.
- A "wait for minimum count" policy accepts either a positive integer (fire when at least N keys have arrived) or a negative integer (fire when at most |N| keys are still missing).
- The default behavior — waiting for all expected keys — remains unchanged when no explicit policy is configured.
- When a configured policy can never be satisfied given the window's expected cardinality, the scheduler logs a warning exactly once per affected DAG/asset combination and does not create a dag run.
- Wait policies must be serializable and survive round-trip encode/decode alongside the mapper configuration.

## Why This Matters

Upstream data pipelines rarely deliver all partitions simultaneously. Requiring 100% completeness before processing downstream adds unnecessary latency. This feature lets pipeline authors tune the trade-off between freshness and completeness directly in the DAG definition.
