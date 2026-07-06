## Description

The Nimby subsystem in the render queue daemon currently has multiple implementation classes — one for Linux device file monitoring and another for cross-platform library-based monitoring — plus a factory that selects between them at runtime. This design is unnecessarily complex and harder to maintain. We should consolidate these into a single unified implementation that always uses the cross-platform input monitoring approach.

Additionally, the attribute that tracks whether Nimby is operational has a misleading name. It currently reports "active," but what it really communicates is whether the system is initialized and ready to run. This has caused confusion since "active" can imply the system is actively doing work, rather than being in a ready state.

## Expected Behavior

- There should be a single Nimby class that handles user activity monitoring via keyboard and mouse input, replacing all the old separate classes and the factory that selected between them.
- The attribute indicating operational readiness should be renamed to clearly signal that the system is "ready" rather than just "active."
- When the input monitoring library is unavailable (e.g., cannot be imported), the system should gracefully set itself as not ready and skip all locking/unlocking behavior when run.
- User interaction should immediately lock the host from rendering, and when the user has been idle for a sufficient threshold period, the host should be unlocked for rendering — but only if resource conditions permit.
- The display environment variable should be automatically configured to a sensible default if not already set in the environment.
- All other parts of the codebase that referenced the old class names or the old readiness attribute should be updated to use the new unified interface.

## Why This Matters

Maintaining multiple Nimby implementations creates unnecessary complexity for contributors. A single unified class is easier to understand, test, and maintain. Renaming the readiness attribute also improves code clarity for anyone reading or extending this code.
