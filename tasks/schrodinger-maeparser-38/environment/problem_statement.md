## Description

The maeparser library currently cannot be built cleanly under strict compiler warning configurations. When strict warnings are enabled — treating warnings as errors — the build fails because of several implicit type-conversion issues and a missing virtual destructor in one of the parser's internal classes.

Specifically:

- The library's boolean property type is an integral type (not a plain boolean), but comparisons in both the implementation and the calling code treat it as if it were a plain boolean. This triggers implicit-conversion warnings.
- One of the library's parser classes defines virtual methods but lacks a virtual destructor, which causes a non-virtual-destructor warning when compiled with the corresponding flag.
- A use of the minimum integer literal in the code triggers a compiler warning in some environments.

## Expected Behavior

- The library should compile without any warnings related to non-virtual destructors, even when that warning is promoted to an error.
- Boolean property values should be retrieved and compared using types that avoid implicit conversion warnings.
- The minimum integer value should be represented in a way that does not trigger compiler warnings.
- Index-keyed property lookups in example usage code should use an unsigned index type rather than a signed one, consistent with the underlying data size.

## Why This Matters

Developers who enable strict compiler warning settings (especially in CI pipelines or cross-platform builds) will encounter build failures with the current code. Fixing these issues allows the library to be compiled cleanly in rigorous build environments and makes it easier to catch real bugs through consistent warning enforcement.
