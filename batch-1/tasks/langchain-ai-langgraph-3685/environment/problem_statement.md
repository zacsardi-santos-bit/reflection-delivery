## Description

When defining a state graph, it is a common and useful pattern to have a rich internal state while exposing a narrower interface to callers — providing a smaller "input" type that only contains the fields a caller needs to supply, and a focused "output" type that only contains the fields the graph should return. LangGraph's state graph supports specifying separate input and output types alongside the full internal state type.

However, when all three (the internal state, the input type, and the output type) are defined as structured validation models, the graph does not work correctly. Specifically, the graph fails to accept instances of the input model directly and does not correctly derive JSON schemas from the narrower input and output models. This breaks the ability to pass a structured input instance to the graph's run and stream methods, and it also breaks checkpointer-based persistence (saving and resuming state) when these structured models are used together.

## Expected Behavior

- A state graph defined with a structured state model and separate structured input and output models should compile and run successfully.
- Running or streaming the graph with an instance of the input model should work correctly, processing the graph and returning results matching the output model.
- The graph's reported input schema should reflect the narrower input model's structure (not the full state), and the output schema should reflect the output model's structure.
- Graphs compiled with checkpointers should correctly save and restore state, support interrupts, resume execution, and allow state updates when the state/input/output types are defined as structured validation models.

## Why This Matters

Developers frequently need to define internal graph state with more fields than callers should be required to provide. The ability to specify separate input and output types is a core part of LangGraph's interface contract, and it should work regardless of whether those types are defined as structured validation models.
