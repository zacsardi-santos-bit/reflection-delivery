## Description

The benchmark tool's dataset-loading code has grown into an unwieldy monolith where all dataset types are imported and re-exported from a single location. As the number of supported dataset types has grown, this makes the module harder to navigate, test, and maintain. It is also not possible to import a specific dataset sampler from its own dedicated submodule — everything must come through the top-level namespace.

We need to reorganize the benchmark datasets code into a proper package structure where each dataset type lives in its own submodule. Additionally, a central registry should be introduced that maps dataset name strings to their respective loaders, so the dispatch logic is data-driven rather than an ever-growing chain of conditionals.

## Expected Behavior

- Each dataset sampler function should be importable from its own dedicated submodule, with each sampler independently accessible from its own location.
- A registry mapping dataset names to loaders should be exported from the top-level package, and must include entries for all currently supported dataset types.
- A unified dispatch function should use this registry to select and run the appropriate loader, and should raise a clear error when an unrecognized dataset name is provided.
- The dispatch function should return the correct row type for each dataset — regular data rows for most datasets, plain dictionaries for the trace-replay dataset type.
- Each sampler must continue to return correctly typed rows: random samplers should support both text and tokenized-ID output; image samplers must include image data; OpenAI-format samplers must preserve optional per-request fields such as temperature and tool definitions.

## Why This Matters

With a registry-based dispatch, adding new dataset types no longer requires modifying a growing conditional block. The clean submodule structure makes each sampler independently discoverable and testable. Callers working with a specific dataset can import only what they need rather than pulling in the entire datasets namespace.
