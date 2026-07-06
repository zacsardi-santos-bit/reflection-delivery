## Description

Two related improvements are needed for SSH certificate and key management:

**1. Private key creation should automatically produce a public key file**

When generating a new SSH private key and writing it to a file path, only the private key file is currently created. In practice, users almost always also need the corresponding public key. Having to run a separate command to extract the public key is an extra burden. The system should automatically create a companion public key file (with the standard public key file suffix) alongside the private key file whenever a key is written to disk. The content of that file should match what you would get by extracting the public key from the private key.

**2. Signing policy should support explicit validity date ranges, not just durations**

The SSH certificate signing policy mechanism currently accepts a TTL (time-to-live) duration to control how long certificates are valid. However, users sometimes want to specify explicit start and end dates for a certificate's validity window instead of or in addition to a TTL. There is currently no support for passing absolute date parameters through the policy merging logic.

When explicit validity dates are provided alongside a policy that enforces a maximum duration, the policy should enforce that limit by trimming the end date if the requested range exceeds it. When any TTL-based calculation is in effect, the system should also store concrete start and end timestamps in the resolved parameters — not just the numeric duration — so downstream consumers have clear, explicit date boundaries.

## Expected Behavior

- Creating a private key to a path also creates a companion public key file in the same location
- The companion file content matches the public key extracted from the generated private key
- Explicit validity start and end date strings can be passed alongside signing policy arguments
- Policy duration limits cap the end date if the requested range is too long
- When a TTL is applied, resolved parameters include both concrete start and end timestamps as formatted strings

## Why This Matters

These changes make key and certificate management more ergonomic and policy-safe: operators get both key files in one step, and signing policies can now reliably enforce validity window limits even when users supply explicit date ranges.
