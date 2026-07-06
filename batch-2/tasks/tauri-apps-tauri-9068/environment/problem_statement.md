## Description

There is a bug in the Access Control List (ACL) resolution system where scopes from multiple capabilities that target different windows are incorrectly merged together. When several capability definitions grant the same command but with different path scopes — for example, one capability allows access to config files and another allows access to resources — the resolver collapses all those scopes into a single entry. This means the combined scope ends up applying globally rather than being properly constrained per-capability or per-window.

## Expected Behavior

- Each individual permission grant for a command should produce its own separate resolved entry, preserving its own scope and window constraints.
- A command allowed by multiple capabilities with different scopes should appear as multiple independent entries rather than being merged into one combined entry.
- Scope identifiers should be stable, sequential integers assigned during resolution rather than values derived by hashing scope content, making the resolved output predictable and deterministic.
- When the same command is granted to different windows by separate capability files, those grants should remain independent entries in the resolved output.

## Why This Matters

This bug causes scopes intended for one window to bleed over into other windows, breaking the principle of least privilege that the capability system is designed to enforce. Developers expect that defining a capability for a specific window with a specific path scope will only allow that scope for that window — not merge it with permissions from unrelated windows or capabilities.
