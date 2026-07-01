## Description

The distributed execution pipeline nodes in Daft currently use generic, internal-sounding names that don't clearly communicate their purpose or distinguish them from their non-distributed counterparts. These names make it hard to quickly understand what type of node you're looking at when debugging or inspecting a distributed query plan, since there is no visual distinction between distributed and non-distributed nodes.

Additionally, there's currently no way to visualize a distributed physical plan — developers can only see a text or diagram representation for the local (non-distributed) plan.

## Expected Behavior

- Each distributed pipeline node type should have a descriptive, "Distributed"-prefixed name that makes its role and context immediately clear, with each node type (in-memory scan, file-based scan, limit, and intermediate) carrying a name that reflects its specific function.
- It should be possible to produce both text-based (ASCII) and diagram-based (Mermaid) visualizations of a distributed pipeline plan.

## Why This Matters

When inspecting query plans or debugging distributed execution issues, it's critical to quickly identify what each node in the pipeline does. Clear, consistent naming makes these names unambiguous and makes the distributed pipeline more observable. Visualization support enables users to understand their distributed query structure in the same way they already can for local plans.
