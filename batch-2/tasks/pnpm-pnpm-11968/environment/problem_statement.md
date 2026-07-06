## Description

When a package's tarball checksum doesn't match the value recorded in the lockfile, the package manager currently logs a warning and silently overwrites the stored checksum, then continues with the install as if nothing happened. This defeats the entire purpose of committing a lockfile: a compromised registry, a republished version with different content, or a tampered proxy could substitute attacker-controlled packages on a clean machine and the tool would simply accept the new content.

## Expected Behavior

- A checksum mismatch against the lockfile should be a hard failure by default, even during a regular (non-frozen) install.
- The force flag should not act as a bypass for integrity verification — it is a routine refresh operation and should not silently overwrite locked checksums.
- A new dedicated opt-in option should be introduced to explicitly allow refreshing locked checksums from the registry (useful after a legitimate republish or stale metadata).
- Using the new checksum-refresh option together with the frozen-lockfile option should be rejected with a clear error, since those two modes are fundamentally incompatible: frozen installs are designed to never modify the lockfile.

## Why This Matters

A committed lockfile is a security control. Silently overwriting its integrity values on any install means the protection only works if you happen to run with frozen-lockfile. Making mismatches fail hard by default closes the gap and ensures that any bypass is an explicit, auditable action.
