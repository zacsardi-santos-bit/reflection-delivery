I'm working on a Terragrunt feature that requires knowing which modules in a stack depend on a given module — not just which modules that module depends on, but the reverse: who depends on me?

I need a function that takes a module stack and produces a reverse-dependency map. The map should have an entry for each module that has at least one dependent, and the value should be an ordered list of those dependents — direct dependents listed before indirect (transitive) ones. For example, if A depends on B and B depends on C, then C's entry should list B first, then A.

The function also needs to handle circular dependency chains gracefully — it should not hang or panic when modules form a cycle.

This reverse-dependency mapping will be used to determine which modules are affected by a change to a given module, so correctness and ordering are both important.
