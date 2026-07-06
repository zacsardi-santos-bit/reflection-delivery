I'm hitting a bug in the decorator transform where a decorated class that extends a base class doesn't correctly handle parent class references in its static members.

*   The decorator transform (using the 2023-11 version of the proposal-decorators plugin) must correctly handle super property accesses in static field initializers of a decorated subclass — e.g., spreading a super static array property must produce the correct merged array at runtime.

*   The decorator transform must correctly handle super property accesses in static accessor initializers of a decorated subclass — reading a super static accessor value must produce the correct inherited value at runtime.

*   The decorator transform must correctly handle super method calls inside private static methods of a decorated subclass — invoking the private method through a public wrapper must return the correct base-class method result.

*   The decorator transform must correctly handle super property accesses and method calls inside static initialization blocks of a decorated subclass — the block must correctly invoke the parent method with the parent property as an argument and assign the result.

*   When the decorator transform processes a decorated class that extends a base class with static members, super property reads must be rewritten to use prototype-chain lookups via the decorator infrastructure's class reference variable (e.g., _get(_get_prototype_of(_Derived), "propertyName", this)).

*   The transformed output for a decorated subclass using super in static members must match the expected output — specifically: Derived.styles equals ["base", "derived"], Derived.value equals "base-accessor", Derived.callSuper() returns "base-method", and Derived.blockValue equals "base:blue".


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.