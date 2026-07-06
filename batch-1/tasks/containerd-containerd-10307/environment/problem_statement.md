## Description

When a container runs inside a Linux user namespace, every UID and GID inside the container is translated to a different UID or GID on the host through one or more ID mapping ranges. Currently, the runtime has no shared utility for performing this container-to-host ID translation. Code that needs to know the host-side identity of a containerized process — for example, when remapping file ownership on a container's snapshot — must either re-implement the mapping logic in place or only support simple single-range cases.

## Expected Behavior

- There should be a utility that accepts a container-side user identity (a UID and GID pair) along with a set of UID mapping ranges and GID mapping ranges, and returns the corresponding host-side UID and GID.
- The utility should correctly handle multiple, non-overlapping mapping ranges.
- When the container UID or GID does not fall within any defined mapping range, the utility must return an error instead of silently producing a wrong value.
- The utility must safely detect and reject cases where the mapping arithmetic would overflow a 32-bit unsigned integer.
- Any computed host ID that equals the maximum 32-bit unsigned integer value must also be treated as invalid and return an error.

## Why This Matters

Without this utility, the runtime either silently computes incorrect host IDs near the integer boundary or is limited to a single mapping range per container, which prevents proper support for more complex user namespace configurations. A correct, shared implementation with proper overflow protection makes the container runtime safer and more capable.
