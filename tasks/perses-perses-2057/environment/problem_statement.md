## Description

The Dashboard-as-Code SDKs (both Go and CUE) include a utility for automatically generating PromQL label filter expressions from dashboard template variables. When variables are configured with filters, the SDK auto-generates the metric selector portion of PromQL queries. Currently, this generation uses exact match operators, which works correctly when a user selects only a single value. However, list-type variables that support multiple selections fail silently — when users select multiple values, they get joined into a combined pattern that only works with regex match operators. The generated queries are therefore broken for any multi-select variable.

## Expected Behavior

- When the filter auto-generation utility builds label matchers for template variables, it should use the regex match operator instead of the exact equality operator. This applies to both text variables and list variables.
- The change must apply in both the Go SDK's label-names and label-values variable builders, and in the CUE SDK's filter utility.
- Example panel definitions should also demonstrate how to build reusable, configurable panel templates that optionally include an aggregation grouping clause, which can be set when instantiating the panel for different contexts.

## Why This Matters

Dashboards that use multi-select variables — allowing users to pick more than one value at a time — currently produce invalid PromQL queries when any variable-aware filter is generated automatically. This makes the SDK-generated label matchers incompatible with multi-select list variables, breaking a core feature of dashboard interactivity for any SDK-authored dashboard.
