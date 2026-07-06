## Description

When a decorated class extends a base class and uses parent class references within its static members, the decorator transform produces incorrect output that breaks at runtime. Specifically, static field initializers that read or spread parent properties, static accessor initializers that copy parent values, private static methods that call parent methods, and static initialization blocks that invoke parent methods — all produce wrong values or fail entirely after transformation.

## Expected Behavior

After the decorator transform, a decorated subclass that uses parent class references in its static members should behave correctly at runtime:

- A static field that spreads a parent static array property should produce the correct merged array
- A static accessor that reads a parent accessor value should return the correct inherited value
- A private static method that calls a parent method should return the correct result when invoked
- A static initialization block that calls a parent method using a parent property as an argument should correctly assign the expected result

## Why This Matters

Decorators combined with class inheritance are a common pattern. When the decorator transform breaks the handling of parent class references in static members, any decorated subclass that relies on parent properties or methods during static initialization will silently produce incorrect values — making this a correctness bug that is difficult to debug.
