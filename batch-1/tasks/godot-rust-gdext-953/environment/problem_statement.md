## Description

When writing Godot extensions in Rust, it's common to need a collection of different Godot objects that all share some common behavior defined by a Rust trait — for example, multiple node types that all support a health or damage system. Currently there is no way to hold a Godot object pointer while also retaining a Rust trait connection: once you abstract over a trait, you lose access to Godot engine methods, the ability to upcast to a parent class, and the ability to pass the object to Godot API calls.

## Expected Behavior

A new smart pointer type should allow developers to:

- Wrap a Godot object in a pointer that simultaneously exposes both Godot class methods and user-defined trait methods
- Acquire shared or exclusive access to the trait interface through guard objects, with the same runtime borrow-checking guarantees as the existing Godot pointer
- Upcast the pointer to a parent Godot class while retaining the trait connection
- Convert the pointer back to the original concrete Godot pointer type, preserving object identity
- Pass the pointer directly to Godot API functions that accept the parent class type

A companion attribute should be provided so that marking a trait implementation block for a Godot class automatically registers the relationship needed by the new pointer type.

All new types and the attribute should be available through the standard prelude import.

## Why This Matters

This enables ergonomic polymorphism across Godot objects from Rust: multiple objects of different Godot classes can be stored under a common Rust interface and still participate in the Godot object lifecycle — being freed, passed to engine APIs, or upcasted — without losing the shared behavior abstraction.
