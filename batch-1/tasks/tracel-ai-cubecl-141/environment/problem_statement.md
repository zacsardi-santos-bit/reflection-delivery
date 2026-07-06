## Description

The kernel launch parameter derive attribute cannot be used on structs that have any named fields containing arrays. When a developer annotates such a struct with the derive attribute, compilation fails with an error indicating that a required internal method is not found on the array type. This means it is currently impossible to group multiple array parameters into a single parameter struct for GPU kernel launches.

## Expected Behavior

- Applying the kernel launch parameter derive attribute to a struct with named fields of array type (e.g., two array fields for left-hand side and right-hand side inputs) should compile successfully.
- The same should work for generic structs where the array element type is a generic float type parameter.

## Why This Matters

Developers often want to bundle related kernel inputs (multiple arrays) into a single named struct rather than passing each one individually. The derive attribute should support this natural pattern. Without it, kernel parameter grouping is restricted to empty or unit structs only, which severely limits the expressiveness and reusability of kernel interfaces.

## Current Behavior

Applying the launch parameter derive attribute to any struct with array-typed fields causes a compilation error: the macro-generated code attempts to call a method on the array type that does not exist, because the array type does not implement the required trait.
