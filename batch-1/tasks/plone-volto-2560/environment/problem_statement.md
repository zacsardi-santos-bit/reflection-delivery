## Description

Right now, every block editor that wants to show an "add block" button has to re-implement the same logic from scratch: checking if the block is empty, managing the open/closed state for the block chooser panel, handling click-outside events to close it, and rendering the button conditionally. This leads to duplicated code across many block components and makes it hard to maintain consistent behavior.

We need a standalone, reusable component that encapsulates all of this: show a button when a block is empty, hide it when the block has content, and open the block chooser when clicked. The component should also support a custom button for cases where the default button style doesn't fit.

## Expected Behavior

- When a block is empty (no value), the component shows a button to open the block chooser.
- When the block already has content, nothing is rendered.
- When the block type is unrecognized, the component should be conservative and render nothing.
- Developers can pass a custom button component to replace the default one.

## Why This Matters

Block editors throughout Volto duplicate this show/hide logic today. Extracting it into a dedicated component reduces duplication, makes each block editor simpler, and ensures all blocks behave consistently when it comes to the block chooser button.
