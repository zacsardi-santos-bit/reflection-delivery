# Add a General-Purpose SNMP Tag Lookup Processor

## Description

Telegraf currently has a processor that enriches metrics with network interface names by performing SNMP lookups, but this is narrowly scoped to a single use case. Users who want to attach other SNMP-derived tags to their metrics — such as device descriptions, interface speeds, or any other data available via SNMP tables — have no flexible, general-purpose way to do this.

We need a new processor plugin that generalizes SNMP-based tag enrichment. Given a metric that carries a tag identifying the SNMP agent address and a tag holding a table row index, the processor should look up the corresponding entries in a configurable SNMP table and attach them as tags to the metric.

## Expected Behavior

- The processor must cache SNMP responses per agent to avoid excessive network traffic
- Cached entries must expire after a configurable time-to-live, triggering a re-query on the next lookup
- When a metric references a table row index not found in the current cache, the processor should handle the miss gracefully: pass the metric through and schedule a re-query if the minimum refresh interval has elapsed
- The processor must support concurrent SNMP lookups across multiple agents with a configurable degree of parallelism
- An optional ordered mode must preserve the original order of metrics in the output, even when SNMP lookups complete out of order

## Related Cleanup

As part of this work, a shared helper that provides sensible SNMP client defaults should be introduced, so both this new processor and the existing interface name processor use the same baseline configuration rather than duplicating it.

## Why This Matters

Users working with SNMP-enabled devices often need more than just interface names. A general-purpose SNMP lookup processor enables enriching any metric with arbitrary SNMP table data, making it much easier to correlate telemetry with device inventory information.
