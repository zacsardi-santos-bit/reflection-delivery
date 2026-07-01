I'm using the schema pruning utility from this library and I've noticed a few cases where the pruner leaves types in the schema that it should be removing.

First, if I define a custom scalar type in my schema but never actually use it in any field, the pruner keeps it around. I'd expect an unused scalar to be cleaned up the same way unused object types or enums are.

Second, if I have several types that all implement the same interface, but only one of them is actually referenced from a query field, the others stick around after pruning. I'd expect any object type that isn't reachable from the root to be removed, regardless of whether it implements an interface.

Third, I'm using the option that lets you supply a custom filter function to protect certain types from being pruned. When I mark a type as "do not prune" using that filter, the interfaces that type implements still get pruned away, which leaves the kept type in a broken state. I'd expect that if a type is kept by the filter, all of the types it depends on — like the interfaces it implements — should also be preserved automatically.
