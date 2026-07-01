I'm working on adding a new lint rule to enforce consistent accessibility modifier usage on TypeScript class members. The rule needs to support three configurable modes: one that requires every member to have an explicit modifier, one that disallows the redundant default-visibility modifier specifically, and one that forbids all accessibility modifiers entirely.

The rule should apply to all member types — properties, methods, constructors, getters, setters, abstract members, and constructor parameters declared inline in the constructor signature. When a violation is found, the diagnostic should clearly explain what is wrong and suggest how to fix it: either to add a modifier or to remove one, depending on the configured mode.

In the mode that requires explicit modifiers, even private class fields using the native JavaScript private field syntax should be flagged as missing an explicit modifier. The default mode should be the one that disallows the redundant default-visibility annotation.

The rule should be placed in the nursery lint group and be configurable via a JSON option key that accepts one of three string values corresponding to the three modes.
