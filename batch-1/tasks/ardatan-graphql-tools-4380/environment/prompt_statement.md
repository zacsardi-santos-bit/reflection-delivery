I'm hitting some gaps in the schema pruning utility and want to get them fixed so pruning actually removes everything it should.

First issue: if I define a custom scalar type in my schema but never reference it from any field reachable from the root, the pruner just leaves it there. I'd expect an unused scalar to get cleaned up the same way unused object types or enums do, so any custom scalar that isn't reachable from a root type should be removed during pruning. Oh and there's an option flag that skips pruning of unused types, and when that's set it should cover unused custom scalars too, keeping them around like the other unused types.

Second: when I've got several types that all implement the same interface but only one of them is actually referenced from a query field, the others stick around after pruning. Any object type that isn't reachable from the root should be removed, even if it implements an interface, since no query path can reach those unreachable implementations.

Third, I use the option that takes a custom filter function to protect certain types from being pruned. When I mark a type as do-not-prune with that filter, the interfaces that type implements still get pruned away, which leaves the kept type in a broken, inconsistent state. I'd expect that if a type is kept because the filter matched it, all the types it depends on, like the interfaces it implements, get preserved automatically too.

Basically I want pruning to be complete and correct, and safe to use alongside the custom type-protection filter.
