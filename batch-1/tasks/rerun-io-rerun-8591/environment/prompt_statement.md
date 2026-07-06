I'm working on the core types crate in Rerun and I need to fix a design problem with the component bundle interface. Right now there's a blanket implementation that makes every single component type automatically satisfy the bundle interface — the one that represents a collection of multiple named component batches. This is dangerous because code expecting a proper multi-component bundle can silently accept a bare single component, which doesn't make semantic sense.

What I'd like to do is remove that blanket implementation entirely, so individual components no longer automatically pose as bundles. I also want to pull the bundle interface definition out of the main library file and into its own dedicated module, keeping the public export path the same.

After this change, if someone wants to pass a single component as a bundle, they should need to be explicit about it: first treat the component as a batch reference, then wrap that in a slice of batch references before using it as a bundle. Slices and arrays of explicit batch references should work naturally as multi-batch bundles.

All the standard collection variants — fixed-size arrays, slices, and vecs — of both raw batch references and bundle-implementing types should still work.
