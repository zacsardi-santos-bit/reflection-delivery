## Description

The cross-media measurement system uses "vid sampling intervals" to define which portion of the viewer ID space to sample when computing reach and frequency measurements. Each interval has a start point and a width. Currently, the system only allows contiguous intervals that fit within the [0, 1] range — meaning the start position plus width cannot exceed 1.0.

This restriction blocks the newer shuffle-based distributed measurement protocol from using "wrapping" intervals that start near the end of the range and continue from the beginning. A wrapping interval like start=0.8, width=0.5 should represent the union of [0.0, 0.3] and [0.8, 1.0], but today the system rejects such values entirely.

## Expected Behavior

- The system should support wrapping sampling intervals (where start + width > 1.0) for the shuffle-based distributed protocol, while the older sketch-based protocol should continue to reject them with a clear validation error.
- A utility function for computing the combined coverage of two sampling intervals should correctly handle wrapping intervals across all overlap scenarios.
- The measurement consumer simulator should accept a configurable sampling interval rather than always using a fixed hardcoded range, enabling test scenarios with wrapping intervals.
- When a wrapping interval is permitted, the sampling width itself must still be at most 1.0.

## Why This Matters

The shuffle-based protocol has different mathematical properties that allow it to work with wrapping sampling intervals. Supporting wrapping intervals enables distributed measurement across a broader set of configurations and is a prerequisite for certain multi-party computation setups. Without this change, the system's validation incorrectly blocks valid configurations for this protocol.
