Implement NAT detection for network traces to identify if NAT translation occurs at any hop along a traced path. Compare the expected checksum of a probe against the echoed checksum from routers to determine NAT status, and update the hop data structure accordingly.

*   Extend the YAML test probe format to include two additional fields:
    *   Format: `{ttl} {status} {rtt} {ip} {sequence} {src_port} {dest_port} {expected_checksum} {actual_checksum}`
    *   `expected_checksum` and `actual_checksum` are u16 values.

*   Update hop data structure to include NAT detection status:
    *   Add `last_nat_status` field to each hop in the expected YAML scenario output.
    *   Possible values: `'no_nat'`, `'nat'`, `'none'`.

*   Modify `crates/trippy-core/src/state.rs`:
    *   Define `NatStatus` enum with variants: `NotApplicable`, `NotDetected`, `Detected`.
        *   Derive `Debug`, `Copy`, `Clone`, `Eq`, `PartialEq`.
        *   Export publicly.
    *   Implement `last_nat_status` method for `Hop` struct:
        *   Signature: `pub const fn last_nat_status(&self) -> NatStatus`
        *   Default value: `NatStatus::NotApplicable`.
    *   Create private function `nat_status`:
        *   Signature: `const fn nat_status(expected: Checksum, actual: Checksum, prev_hop_checksum: Option<u16>) -> (NatStatus, u16)`
        *   Logic:
            *   First responding hop (no previous checksum): 
                *   If `expected == actual`, return `(NatStatus::NotDetected, actual.0)`.
                *   Otherwise, return `(NatStatus::Detected, actual.0)`.
            *   Subsequent hops:
                *   If `actual.0 == prev`, return `(NatStatus::NotDetected, prev)`.
                *   Otherwise, return `(NatStatus::Detected, actual.0)`.

*   Define `Checksum` struct in `crates/trippy-core/src/types.rs`:
    *   `pub struct Checksum(pub u16)`
    *   Derive `Debug`, `Clone`, `Copy`, `Default`, `PartialEq`, `Eq`, `Ord`, `PartialOrd`.

*   Extend `ProbeComplete` struct in `crates/trippy-core/src/probe.rs`:
    *   Add fields: `expected_udp_checksum: Option<Checksum>`, `actual_udp_checksum: Option<Checksum>`.
    *   Update `Probe::complete()` / `Probe::new()` to accept these fields.

*   During state update, process completed probes:
    *   If both checksum fields are `Some`, call `nat_status` and store the result in `last_nat_status`.
    *   Track `prev_hop_checksum` through probe iteration to manage checksum comparison across hops.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.