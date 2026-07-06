## Description

The TrueType hinting engine is missing dispatch support for several instructions that were implemented but never wired into the main execution loop. Specifically, the instructions for writing to and reading from storage, measuring rendering parameters, and manipulating glyph outline points are all commented out in the dispatch table. When a font program encounters these instructions, the engine fails with an "unhandled opcode" error instead of executing the intended operation.

## Expected Behavior

- The engine should correctly dispatch and execute the write-to-storage instruction, allowing font programs to store values that can be retrieved with the corresponding read-from-storage instruction.
- Instructions for writing and reading from the control value table should be properly dispatched.
- Instructions for measuring rendering parameters (pixels per em, point size) should be wired up and return accurate values from the current graphics state.
- Instructions for modifying glyph outline point properties (flipping on/off curve status for individual points or ranges, and untouching points) should be implemented and dispatched correctly.
- The engine's graphics state should track whether horizontal and vertical interpolation passes have been completed, since some outline-modifying instructions must be skipped after interpolation.

## Why This Matters

Font rendering depends on executing TrueType hint programs faithfully. Any instruction silently dropped or returned as an error results in incorrectly hinted glyph outlines. These missing instructions are commonly used in production fonts, so connecting them is necessary for correct rendering behavior.
