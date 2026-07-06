## Description

The library currently only supports providing coordinate reference system (CRS) information as a full structured JSON object (PROJJSON). There is no way to specify a CRS using a short authority string such as a well-known authority identifier. Passing such a string either fails silently or is rejected entirely.

Additionally, when serializing geometry type metadata, the library does not distinguish between a full structured CRS object and a plain string-based CRS — making it impossible for consumers of the metadata to reliably know which format was used. Round-tripping of type metadata is therefore not reliable across CRS formats.

## Expected Behavior

- Users should be able to pass a plain string (e.g., an authority code) as a CRS value anywhere in the library that currently accepts a full structured CRS object.
- A new CRS type that wraps a plain string should be available and usable in the type system.
- When serializing type metadata, the library should include a discriminator field that identifies structured CRS objects, so consumers can tell the difference between a string-based CRS and a PROJJSON object.
- When deserializing type metadata, the library should restore the correct CRS type: string-based CRS values without a discriminator field should become the new string CRS type; structured CRS values with the discriminator should become the existing structured CRS type.
- When a raw string is assigned as a CRS value, it should be automatically converted to the appropriate string CRS wrapper.

## Why This Matters

Many CRS specifications are naturally expressed as short authority codes (like those used in geospatial software). Requiring users to provide a full PROJJSON dictionary is unnecessarily burdensome when they just want to reference a well-known CRS by its authority string. This change also ensures that metadata round-trips are stable and unambiguous across CRS formats.
