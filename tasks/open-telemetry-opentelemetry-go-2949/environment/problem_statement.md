## Description

The metric SDK's reader types currently have no way to specify the temporality model they use when reporting measurements. Temporality controls whether metric data is reported as running cumulative totals or as per-interval delta values — and different metric backends require different approaches. Without this configurability, users cannot align the SDK's output with their backend's expectations at reader construction time.

## Expected Behavior

- When creating a manual reader or a periodic reader, the caller should be able to pass an option that specifies a temporality selector — a function that maps an instrument kind to the desired temporality
- If no temporality option is provided, the reader should default to cumulative temporality
- If multiple temporality options are provided, the last one should take effect (later settings overwrite earlier ones)
- Both reader types should expose a way to query the configured temporality for a given instrument kind

## Why This Matters

Different observability backends expect metric data in different forms. Some backends require delta values (change since the last collection), while others expect cumulative totals. Allowing each reader to be configured with a preferred temporality at creation time — rather than hardcoding a single global default — makes the SDK usable with a wider variety of backend exporters without requiring workarounds.
