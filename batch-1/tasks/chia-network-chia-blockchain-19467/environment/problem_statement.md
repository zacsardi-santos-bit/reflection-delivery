## Description

The peer address manager currently persists known network peers using a text-based serialization format. We need to migrate to a more compact and efficient binary format while ensuring backward compatibility with existing installations that already have peer data in the old format.

## Expected Behavior

- The address manager should be able to serialize its current peer list into a compact binary representation and return those bytes directly.
- Loading peer data should work seamlessly whether the file is in the old text-based format or the new binary format — existing nodes should migrate transparently without losing any peer data.
- If no peers file exists (e.g., a fresh install or the file was deleted), loading should return an empty address manager rather than raising an error.
- If the stored binary data contains entries with an unrecognized address type, those malformed entries should be skipped gracefully. The loader should still return a usable address manager (with just the valid entries), not crash.
- IPv6 peer addresses must be correctly handled through the full serialize/deserialize cycle, preserving their address, source, and timestamp.
- The address manager module should export the bucket size and bucket count constants so other parts of the system can use them.

## Why This Matters

The text-based format is verbose and slower to read/write. Switching to a binary format reduces disk usage and speeds up startup. Graceful handling of missing files and corrupted entries makes the system more robust in production deployments. Transparent migration ensures users upgrading from older versions do not lose their peer lists.
