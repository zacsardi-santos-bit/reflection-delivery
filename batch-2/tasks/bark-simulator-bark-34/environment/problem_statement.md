# Add Lane Type Support and Decouple Lane Creation from Plan View

## Description

Currently, lane objects in the simulator do not carry any information about what kind of road feature they represent — there is no concept of whether a lane is a driving surface, a sidewalk, a border strip, or any other category. As a result, the road parser only processes driving-type lanes and silently ignores all others. This makes it impossible to accurately model urban roads that contain multiple lane categories (for example, a road with both driving lanes and sidewalks on each side).

Additionally, creating a lane is currently done by calling a method on the plan view object, which tightly couples lane geometry construction to the road's central reference line. This prevents creating lanes whose geometry is offset from an adjacent lane rather than from the road centerline — a requirement for correctly computing the positions of outer lanes in multi-lane cross-sections.

## Expected Behavior

- Lane objects must carry a type attribute (driving, sidewalk, border, etc.) that can be set during parsing and queried afterwards.
- Lane creation should be available as a standalone operation that takes any reference line as input, not only the road plan view's central line. This allows outer lanes to be offset from their inner neighbor.
- The road parser should recognize all supported lane type categories (including sidewalks and borders), assign the correct type to each created lane, and skip only truly unsupported types.
- Visualization code should be able to distinguish lane types and render them differently (e.g., driving lanes in grey, sidewalks in green).

## Why This Matters

Without lane type information, the simulator cannot distinguish different physical road features, leading to incomplete or incorrect road representations for urban scenarios. Accurate multi-lane urban roads — with sidewalks, borders, and driving surfaces — are essential for realistic simulation of urban driving.
