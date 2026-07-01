## Description

The out-of-band resource manager component is currently located under a deeply nested path within the broader resource manager subsystem. This makes it harder to discover and work with compared to other top-level agent components. The component deserves its own dedicated package location that reflects its standalone role in the agent.

## Expected Behavior

- The out-of-band resource manager and all its sub-components (checkpoint management, endpoint lifecycle, executor, and metadata manager) should be accessible at a dedicated, shorter top-level package path under the agent directory.
- All internal cross-references between the sub-packages should continue to resolve correctly at the new location.
- The agent entry point that initializes this component should reference the new location.
- All existing functionality — including checkpoint state management, endpoint creation and removal, CPU set commitment, container resource updates, resource name mapping, and container skip logic — must continue to work identically after the move.

## Why This Matters

Having the component at a deeply nested, non-obvious path adds unnecessary friction for contributors trying to find or extend it. Moving it to a top-level location with a clear, concise name improves the overall project structure and makes the agent's architecture easier to navigate.
