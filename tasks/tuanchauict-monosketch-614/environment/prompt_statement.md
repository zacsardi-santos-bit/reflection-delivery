I'm working on the Svelte/TypeScript port of MonoSketch and need to implement a group shape type that can hold and manage an ordered collection of child shapes. Right now there's no way to group shapes together as a single container.

The group needs to support adding shapes at specific positions — at the end by default, at the beginning, or after a particular existing shape. It should enforce ownership: if a shape already belongs to a different group, adding it to another group should be silently rejected. When a shape is successfully added, the group should automatically take ownership of it. Adding a shape that's already in the group should have no effect.

The group also needs to support removing shapes and reordering them: moving a shape one step up or down in the stacking order, or jumping it all the way to the top or bottom.

Additionally, the rectangle shape is currently not exported and its constructor requires an explicit id argument even when none is needed. I need to make the rectangle publicly accessible, allow creating it without needing to provide an explicit identifier, and add a convenient factory method for constructing one from a plain object where the identifier and parent reference are both optional.
