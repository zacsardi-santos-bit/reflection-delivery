## Description

There are two related but broken behaviors when combining global upgrade flags with per-package upgrade flags in the dependency compilation tool:

1. **Global upgrade + per-package constraint is ignored**: When you run a global upgrade but also specify that a particular package should be kept below a certain version (e.g., "upgrade everything but keep this library at version 1.x"), the version constraint is silently ignored. The package gets upgraded without any upper bound.

2. **No-upgrade + per-package upgrade is ignored**: When you explicitly disable upgrades globally but want to make an exception for one specific package, the exception is silently discarded. The package stays pinned even though you explicitly requested it be upgraded.

## Expected Behavior

- When upgrading all packages while specifying a version constraint for one package, the constrained package should resolve to its latest version that satisfies the constraint. Other packages should upgrade freely.
- When disabling upgrades globally while specifying a package to upgrade, only that specific package should be upgraded. All other packages should remain at their pinned versions.
- The settings representation for upgrade configuration should cleanly separate "which packages to upgrade" from "what version constraints apply to those packages." These are distinct concerns and should be modeled separately.

## Why This Matters

Users reasonably expect that combining these flags should work as described. The current behavior silently ignores user intent, which is confusing and leads to unexpected resolution results. This affects anyone who wants fine-grained control over dependency upgrades.
