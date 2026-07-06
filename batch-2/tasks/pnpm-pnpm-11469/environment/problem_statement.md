## Description

When the workspace configuration file is updated programmatically (e.g., when a new catalog, overrides, or allowBuilds section is added), the tool does not preserve the formatting style of the existing file. Specifically, blank lines that authors use between top-level sections are lost or incorrectly applied, and the alphabetical ordering logic for top-level keys does not handle all valid layouts.

## Problems

1. **Blank lines not preserved**: If the original workspace manifest uses blank lines between top-level sections as visual separators, and a new section is inserted or appended, the blank lines should be carried over. Currently they are ignored, resulting in a compacted file that looks different from the original.

2. **Incorrect alphabetical ordering for top-level keys**: The tool should detect whether the existing top-level keys are in alphabetical order and, if so, insert new keys in sorted position. However, the detection fails when all keys including the packages list are sorted alphabetically (rather than placing packages first by convention). In that case, new keys are incorrectly appended to the end.

3. **Existing key order not preserved within sections**: When updating a section that has keys in a non-alphabetical custom order, the tool should preserve the original key ordering and only append new keys at the end. Currently the existing key order may be discarded.

## Expected Behavior

- Blank lines between top-level sections are preserved when inserting or appending new sections.
- No blank lines are added if the original file had none between sections.
- New top-level keys are sorted into their correct position when the existing layout is alphabetically ordered (regardless of where the packages entry appears).
- Existing keys within a section retain their original order; new keys are appended after them.

## Why This Matters

Automated updates to the workspace manifest should produce diffs that look intentional and minimal. Unexpected formatting changes (removed blank lines, reordered keys) make configuration updates harder to review and can interfere with the author's own organizational style.
