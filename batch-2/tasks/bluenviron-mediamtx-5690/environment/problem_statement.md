## Description

The streaming server's metrics endpoint currently reports only a total reader count per path, placed in the deprecated metrics section. There's no way to tell from the metrics how many readers are using each protocol type — for example, how many are connected via RTSP versus RTMP. This makes it hard to profile or monitor connection patterns.

## Expected Behavior

- The metrics endpoint should expose a per-type reader count metric in the **primary** metrics section, breaking down active readers by their connection protocol type.
- When a path has readers of multiple types, there should be a separate metric line for each type, labeled accordingly, with the count for that type.
- Multiple lines for the same path should appear sorted alphabetically by connection type.
- The total reader count metric that currently lives in the deprecated section should be removed from the deprecated section (the new per-type metric in the primary section replaces it).

## Why This Matters

Operators monitoring a live streaming server often need to understand not just how many viewers are watching a stream, but which protocols they're using. With the current single-count metric in the deprecated section, this visibility is entirely absent. The new per-type metric allows dashboards and alerting systems to break down reader connections by protocol.
