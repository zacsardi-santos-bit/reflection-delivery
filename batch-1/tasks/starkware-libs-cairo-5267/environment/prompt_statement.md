I'm working on a refactoring of the Sierra code generator in the Cairo compiler. Right now, every code generation function returns a list of statements that the caller has to manually collect and merge into a larger list. This is verbose and error-prone, especially when it comes to attaching source location information — each call site has to remember to propagate location data onto the statements it receives.

I'd like to change this so that the generator context acts as a statement accumulator. Instead of returning statement lists, generation functions should push their output directly into the context. The context should expose a method to retrieve all the accumulated statements once generation is complete. Source location information should be applied centrally when statements are pushed to the context, rather than being scattered across individual call sites.

As part of this, the helper functions that construct individual statement values should be updated to return plain statement objects rather than location-wrapped ones, since location is now handled by the context.

This refactoring should cover block code generation, statement code generation for all statement kinds, match handling, return code generation, and all the internal helpers like duplicate-variable insertion and drop insertion.
