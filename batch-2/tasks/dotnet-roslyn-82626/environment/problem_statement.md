## Description

The C# compiler supports a new "union" type declaration syntax, but it currently does not enforce member restrictions that are inherent to how unions work. A union's synthesized storage and synthesized conversion constructors place fundamental constraints on what members can appear in a union body — yet today the compiler accepts any member without complaint, leading to invalid declarations that will fail or misbehave at a later stage.

There are three categories of restrictions that need enforcement:

1. **Instance storage members**: Union declarations must not contain instance fields, auto-properties (which implicitly back their value in a field), properties with an implicit backing field, field-like events (events without explicit accessor bodies), or explicit interface implementations of auto-properties. Static variants of these members are fine and should continue to compile without errors.

2. **Single-parameter constructors**: Because a union automatically synthesizes one-parameter conversion constructors for each of its case types, explicitly declaring a public constructor with a single parameter conflicts with this synthesis. Such declarations should be rejected.

3. **Constructor chaining**: Constructors in a union must explicitly chain to another constructor (synthesized or explicitly declared) by using a this-initializer with arguments. A constructor that lacks any this-initializer, or one that chains to the default parameterless constructor, is invalid because it bypasses the union's initialization contract.

## Expected Behavior

- A union declaration containing an instance field, auto-property, field-backed property, or field-like event should produce a compile-time error identifying the offending member.
- A union declaration containing an explicitly declared constructor with exactly one parameter should produce a compile-time error identifying that constructor.
- A union declaration containing a constructor without a proper this-initializer (or with one that calls the default constructor) should produce a compile-time error.
- All errors should report at the location of the offending member for easy diagnosis.
- Valid members (static fields, static properties, non-field-backed instance properties, explicitly-accessored events, multi-parameter or zero-parameter constructors, and constructors with valid this-initializers) should continue to compile without errors.

## Why This Matters

Without these checks, developers writing union declarations receive no feedback when they accidentally include incompatible members. The errors would only surface at runtime or in obscure later compilation stages, making union declarations difficult to use correctly. Clear, early diagnostic messages allow developers to discover and fix these mistakes immediately.
