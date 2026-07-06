# Add constructor functions for M-coordinate points and linestring from WKB

## Description

Apache Sedona is missing support for two common geometry constructor operations that users expect from a full-featured spatial library:

1. **Point with M coordinate**: Users need to construct Point geometries that carry a measure (M) value alongside the X and Y coordinates. Currently only plain 2D and 3D (Z) points are supported as constructor functions. The resulting geometry's text representation should follow the standard WKT M-coordinate point format.

2. **Linestring from WKB**: Users need a dedicated function to create a Linestring geometry from Well-Known Binary (WKB) data, supplied either as raw binary bytes or as a hex-encoded string. Unlike the general geometry-from-WKB constructor, this one should be linestring-specific: it should return null when the WKB represents a different geometry type (such as a point or polygon), and should raise an error when the input is not valid WKB at all. An optional spatial reference ID (SRID) parameter should also be supported, with the assigned SRID reflected in the geometry's extended WKT output.

## Expected Behavior

- Creating a point with x, y, and m values produces a geometry whose text representation follows the standard WKT format for M-coordinate points.
- Parsing linestring WKB from binary bytes or a hex string returns the correct Linestring geometry.
- Parsing WKB that represents a non-linestring geometry returns null without raising an error.
- Providing an invalid (non-WKB) input raises an exception.
- When an SRID is provided to the linestring WKB constructor, the SRID is attached to the geometry and appears in extended WKT output.
- Both functions are available in Spark SQL, the DataFrame API, Flink, Snowflake, and the core Java library.

## Why This Matters

Many GIS datasets use M-coordinate points for linear referencing (e.g., measuring distance along a route), and WKB is one of the most common binary serialization formats for exchanging geometries. Having dedicated, type-specific constructors makes it easier to work with these data formats correctly and safely.
