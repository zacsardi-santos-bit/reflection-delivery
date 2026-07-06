## Description

The tensor alignment and unsharding utilities currently track which dimension represents "tokens," "heads," or other semantic axes by passing integer index parameters alongside tensors at call sites. This is fragile — callers must compute and pass these indices explicitly every time, and the meaning of each dimension is not encoded in the tensor itself. We should move to using named tensor dimensions so that tensors carry their own dimension metadata, and the internal processing code can look up the right dimensions by name automatically.

## Expected Behavior

- A new utility function should accept a dimension specification string and return only the plain dimension names, stripping any modifier annotations.
- A new utility function should resolve a named dimension's integer position in a named tensor, raising an informative error if the dimension is not found or if the tensor has no names at all.
- New utility functions should make it easy to apply a list of names to a tensor and to strip all names from a tensor (including from already-unnamed tensors, without errors).
- The dimension specification type should be exported so callers can extract dimension names from parsed specifications.
- The concatenation and reordering parameter types should store string dimension names instead of integer indices.
- Functions that previously required callers to pass explicit token dimension index parameters should instead infer the token dimension from the tensor's named dimensions, removing those parameters from the public API.
- The aligner plan structure should no longer carry a token dimension field.
- Functions that consume named tensors should accept and return named tensors, with callers responsible for stripping names when interoperating with code that expects plain tensors.

## Why This Matters

Storing dimension meaning as integer indices scattered across call sites makes the code hard to maintain and error-prone. By encoding dimension identity in the tensor itself and resolving positions by name, the system becomes more self-documenting and less prone to off-by-one mistakes when tensor layouts change.
