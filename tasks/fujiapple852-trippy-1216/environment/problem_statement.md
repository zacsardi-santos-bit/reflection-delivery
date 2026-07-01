## Add NAT Detection for Network Traces

### Description

When tracing a network path, each intermediate router sends back an error message that includes an echo of the original probe's transport-layer headers, specifically including the checksum. If a NAT (Network Address Translation) device exists along the path, it rewrites these transport-layer fields — meaning routers beyond the NAT device will echo back a different checksum than what was originally sent.

Currently, Trippy has no way to detect or surface this information to users. There is no indication in the trace output of whether NAT translation is occurring at any hop along the path.

### Expected Behavior

- Each hop in the trace should carry a NAT detection status
- For the first hop that responds, compare the expected checksum of the probe against the checksum that was echoed back. If they differ, flag the hop as having detected NAT; if they match, flag it as no NAT detected
- For subsequent hops, compare the echoed checksum against the checksum value from the previous responding hop. If they differ, flag it as detected; if they match, flag it as not detected
- Hops that received no probe responses should be marked with a "not applicable" status, indicating NAT detection could not be performed
- The NAT status should reflect only the most recently received probe for each hop

### Why This Matters

Users debugging connectivity issues or analyzing network topology need visibility into where NAT translation is occurring along a traced path. Without this, users cannot tell whether packet header modifications are happening, which can affect how probes are routed and how responses are interpreted.
