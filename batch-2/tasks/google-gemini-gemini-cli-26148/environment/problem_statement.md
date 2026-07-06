## Description

When the CLI renders a group of tool calls that includes a topic-update separator in the **middle** of the group (with regular tools both before and after it), the tools appearing after the separator are displayed without a top border. This makes the layout look broken — the bordered box framing for the second group of tools is missing its top edge.

## Expected Behavior

- When a topic-update tool appears between two regular tool calls, the rendering should show:
  - The preceding tool(s) in a properly closed bordered box
  - The topic heading as a visual separator
  - The following tool(s) in a new, fully bordered box (with top border intact)
- This should work regardless of whether the topic-update tool appears at the start, middle, or end of a sequence

## What Currently Happens

The component already handles topic-update tools correctly when they appear at the beginning or as standalone calls. However, when a topic-update tool is sandwiched between other tools, the tool group immediately following it loses its top border, resulting in a visually incomplete frame.

## Why This Matters

Users see an inconsistent and visually broken UI when the model interleaves topic markers with tool actions. The separator should cleanly divide the preceding and following tool groups, each rendered with proper borders on all sides.
