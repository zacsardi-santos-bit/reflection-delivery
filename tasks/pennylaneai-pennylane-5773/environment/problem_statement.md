## Description

PennyLane currently supports importing fermionic operators from a widely-used external quantum chemistry library, but there is no way to convert PennyLane operators back to that library's format. This makes it difficult to work in workflows that require round-tripping between the two libraries or exporting PennyLane results for use with external tools.

Additionally, there is no support for importing qubit-level operators from the external library — only fermionic ones — leaving users who work with qubit operators without a direct conversion path.

## Expected Behavior

- A new conversion function should be added that accepts PennyLane's fermionic word, fermionic sentence, and qubit operator types and converts them to the corresponding representation in the external library.
- The function should support optional wire remapping (via a mapping of old wire labels to new ones), so that users can control how orbital or qubit indices are mapped during export.
- The function should support a tolerance parameter that controls whether small imaginary parts of coefficients are dropped or retained in the output.
- An import path should be added for qubit operators from the external library, with support for specifying the desired PennyLane output format (linear combination or sum type) and a tolerance for handling near-real complex coefficients.
- A utility to convert a fermionic operator word to a human-readable string should be provided, supporting both PennyLane's native notation and the external library's notation.
- Appropriate errors should be raised for unsupported operator types, invalid format options, incomplete wire mappings, and when the external library is not installed.

## Why This Matters

Users doing quantum chemistry simulations often need to move operators between PennyLane and external tools. Without two-way conversion, this requires manual translation. Adding export functionality alongside the existing import closes the gap and enables fully round-trippable workflows.
