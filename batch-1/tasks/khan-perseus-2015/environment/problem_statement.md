# Refactor Numeric Input Editor: Improve Accessibility and Remove Toggle

## Description

The numeric input editor widget in the content editor has a few usability and accessibility problems. Several configuration options are hidden behind a "Toggle options" control, meaning content authors have to click a link to reveal advanced settings before they can use them. This is unnecessarily cumbersome and makes the interface harder to discover. Additionally, the controls for setting options use an inconsistent mix of standalone buttons and checkboxes without logical groupings, making it harder for assistive technologies to communicate context to users.

## Expected Behavior

- All configuration options should be directly visible without any toggle or expand/collapse mechanism
- Mutually exclusive options (like input width, alignment, number style, answer format strictness, and unsimplified answer handling) should be organized into clearly labeled groups of radio buttons
- Each group should have a meaningful accessible name so users (especially those relying on screen readers) understand what the group of options controls
- Answer format checkboxes should remain as checkboxes but should also be directly accessible without toggling

## What Should Change

- The "Toggle options" control should be removed; all settings should always be visible
- Width options should be presented as a labeled radio group
- Alignment, number style, and answer format strictness options should each be in their own labeled radio group
- Unsimplified answer handling options should be presented as a labeled radio group

## Why This Matters

Content authors use this editor frequently. Hiding options behind a toggle slows down their workflow and makes the UI less predictable. Better grouping with clear labels also improves screen reader accessibility, which is important for ensuring the editing tools are usable by everyone.
