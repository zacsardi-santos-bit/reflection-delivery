## Description

The extension management system has no mechanism to detect whether an extension's installation data has been tampered with between installation and an update. If someone modifies the recorded source location or configuration of an installed extension after the fact, the system will happily apply the forged update without any warning. This is a potential supply-chain security risk.

Additionally, when loading extensions at startup, broken or invalid extension installations produce inconsistent behavior: some errors go through the wrong logging channel, the messages say "skipping" rather than describing what actually happens (removal), and orphaned installation directories from failed or corrupted linked extensions are left behind — preventing re-installation until the user manually cleans them up.

On macOS, the keychain availability check can return incorrect results when the default keychain database file is missing from disk. Instead of gracefully falling back to an alternative storage mechanism, the system may attempt to use a broken native keychain.

## Expected Behavior

- When an extension is installed or updated, a cryptographic signature of its installation source metadata should be stored securely (using the system keychain where available, with a file-based fallback).
- Before performing any extension update, the system should verify that the stored signature matches the current metadata. If tampering is detected, the update should be blocked with a clear error message.
- If no integrity record exists yet (first update after this feature is deployed), the system should proceed with the update and establish the initial trust record.
- At startup, any extension installation that points to a non-existent source should be automatically removed so the user can re-install cleanly.
- Warnings about broken extensions during loading should use a consistent format that names the extension and describes what action was taken (removal), and should be routed through the standard warning channel rather than the error channel.
- On macOS, before using the native keychain, the system should verify that the default keychain database file actually exists on disk, and fall back to file-based key storage if it does not.
- The extension update command should prompt the user for confirmation before proceeding.

## Why This Matters

Without integrity verification, a compromised extension record could redirect an update to a malicious source without the user's knowledge. Automatic cleanup of orphaned installations removes a common frustration where users cannot re-install an extension after a failed or manually removed installation. Better warning messages make it easier to understand what the system did and why.
