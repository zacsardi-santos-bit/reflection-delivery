## Description

TypeScript allows developers to annotate class members with access modifiers (marking them as visible only within the class, to subclasses, or to everyone). However, there is currently no lint rule to enforce a consistent policy on whether these modifiers should always be present, never appear for the default visibility, or be entirely forbidden. This leads to mixed styles across a codebase: some classes annotate all members, others omit annotations, and others use the redundant annotation for the default visibility level.

## Expected Behavior

A new configurable lint rule should enforce one of three consistency policies on class member accessibility modifiers:

- **Explicit**: Every class member (properties, methods, constructors, getters, setters, and constructor parameters) must have an explicit accessibility modifier. When a member is missing one, the developer is told to add one.
- **No-public**: The annotation for the default visibility level is redundant and should be omitted. When a member uses this modifier, it is flagged and the developer is told to remove it.
- **None**: No accessibility modifiers are allowed on any class member at all. When any modifier is present, the developer is told to remove it.

The rule should cover all member types: regular properties and methods, constructors, getters, setters, abstract members, and constructor parameter shorthand.

## Why This Matters

Consistent accessibility annotations help teams communicate design intent clearly — either by making every member's visibility explicit or by eliminating noise from redundant annotations. Without enforcement, codebases drift into inconsistency that makes it harder to reason about class interfaces.
