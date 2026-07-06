Extend the interactive graph editor's start coordinates settings panel to support segment and linear system graphs. Implement UI sections for each segment or line, allowing authors to edit initial positions. Ensure the panel heading is collapsible and accessible.

*   Update `StartCoordSettings` component in `packages/perseus-editor/src/components/start-coord-settings.tsx`:
    *   Support `type="segment"`:
        *   Accept `numSegments` prop.
        *   Render sections labeled "Segment 1", "Segment 2", etc.
        *   Include "Point 1" and "Point 2" subsections with coordinate pair spinbutton inputs.
    *   Support `type="linear-system"`:
        *   Render sections labeled "Line 1" and "Line 2".
        *   Include "Point 1" and "Point 2" subsections with coordinate pair spinbutton inputs.
    *   Implement a button with accessible name "Use default start coords" to reset coordinates to defaults.
    *   Render "Start coordinates" heading as an accessible button element (role="button") to toggle visibility of settings contents.

*   Implement coordinate change handling:
    *   For segment graphs:
        *   When a coordinate input changes, call `onChange` callback with updated `CollinearTuple[]` array.
        *   Use formula: `segmentIndex * 4 + pointIndex * 2 + coordIndex`.
    *   For linear-system graphs:
        *   When a coordinate input changes, call `onChange` callback with updated `CollinearTuple[]` array.
        *   Use formula: `lineIndex * 4 + pointIndex * 2 + coordIndex`.

*   Implement reset functionality:
    *   For segment graphs, clicking "Use default start coords" button calls `onChange` with default coordinates: `[[[-5, 5], [5, 5]], [[-5, -5], [5, -5]]]`.
    *   For linear-system graphs, clicking "Use default start coords" button calls `onChange` with default coordinates: `[[[-5, 5], [5, 5]], [[-5, -5], [5, -5]]]`.

*   Implement utility functions:
    *   In `packages/perseus-editor/src/util/object-utils.ts`, export a `clone` function:
        *   Signature: `clone<T>(obj: T): T`
        *   Perform deep copy using JSON serialization.
        *   Throw an error if object cannot be serialized.
    *   In `packages/perseus/src/widgets/interactive-graphs/reducer/initialize-graph-state.ts`, export and re-export:
        *   `getSegmentCoords` to compute default starting coordinates for segment graphs.
        *   `getLinearSystemCoords` to compute default starting coordinates for linear system graphs.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.