## Description

Logkit's transform pipeline lets users reshape log records as they flow through the system, but there is currently no transformer that simply stamps a fixed, static annotation onto every record. Users have no built-in way to attach constant metadata — like an environment name, datacenter, or service identifier — to all log entries at transform time.

## Expected Behavior

- A new transformer should accept a field name and a fixed value, and add that field to every log record it processes.
- If the target field does not yet exist in a record, it should be added with the specified value.
- If the field already exists, the transformer should by default refuse to overwrite it and track how many records were skipped due to this conflict, returning an error to signal the problem.
- The transformer should also support an explicit override mode: when enabled, it replaces any existing value for that field rather than skipping the record.
- The transformer should run at the stage after parsing, consistent with other mutate-style transforms.

## Why This Matters

Without this capability, users who want to tag all log records with a static label must add custom logic outside of logkit or use workarounds. A dedicated labeling transformer makes this common use case simple, safe, and configurable.
