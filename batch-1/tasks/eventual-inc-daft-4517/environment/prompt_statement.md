I'm working on Daft's distributed execution engine and I'd like to improve how pipeline nodes identify themselves and how the distributed plan can be visualized.

Right now, the distributed pipeline nodes have generic internal names that don't make it clear they belong to the distributed execution layer. I'd like each node type to have a descriptive name with a "Distributed" prefix that clearly reflects its specific role — so the in-memory scan node, file-based scan node, limit node, and intermediate processing node each get an appropriately prefixed, self-describing name.

Beyond renaming, I'd also like distributed pipeline nodes to support visualization in both ASCII text and Mermaid diagram formats, similar to what's already supported for local physical plans. This would mean each node type can describe itself for display purposes, and there should be top-level functions to render the whole distributed pipeline as either an ASCII tree or a Mermaid diagram.

There are also some related internal changes needed: the methods that create work tasks should be updated to accept collections of inputs rather than a single item at a time, and the execution configuration should be carried as part of the plan structure rather than being threaded through each layer separately at execution time.
