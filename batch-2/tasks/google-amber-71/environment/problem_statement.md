So I'm cleaning up numeric type inconsistencies in our C++ graphics testing framework, and they're both causing compiler warnings (which fail the build since we treat warnings as errors) and some genuine precision problems at runtime. A few things I keep hitting.

First, some accessors that logically return integer quantities are being treated as floats. The stencil buffer clear value and the count of control points in a patch command are the big ones, they get compared as if they were floating-point numbers when they should return proper unsigned integer types. That creates pointless type conversions and mismatch warnings.

Then there's a real precision issue with converting large 64-bit integer token values to double. When you take the minimum or maximum representable 64-bit signed integer and convert it, then read it back as a double, the result loses accuracy. Feels like there's an intermediate single-precision step sneaking in somewhere, and it should preserve the full double-precision value instead.

Same flavor of bug with tolerance threshold values used in comparisons, they're getting stored as single precision rather than double, so comparing against things like 0.5 or 5.0 picks up rounding errors. Store those as double so the equality checks stay clean.

Oh and there's a whole pile of single-precision float accessors, covering geometry coordinates, color components, depth and stencil values, and various pipeline state parameters, that are being compared against double-precision literals in tests. That triggers implicit-conversion warnings on strict compilers. I want those cleanly comparable against single-precision float literals so no implicit conversion happens.

Net goal: integer-valued accessors return integers, single-precision accessors compare with single-precision literals, double-precision values actually retain double precision, and the build comes out clean under strict warning settings.
