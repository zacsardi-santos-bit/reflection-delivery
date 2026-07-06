# Red Team Setup Wizard: Responsive Layout and Accessibility Improvements

## Description

The red team setup wizard does not adapt well to narrow or mobile-sized screens. Many panels, form rows, dialogs, and tab strips display their elements side-by-side regardless of available screen width, causing overflow and layout problems on small devices. The wizard should stack elements vertically on narrow screens and switch to a horizontal arrangement when enough space is available.

Additionally, numerous interactive action buttons throughout the wizard — including buttons to remove, configure, edit, and view documentation for items — carry no descriptive accessible name. Screen reader users cannot determine which item a button acts on or what it will do. Every button that acts on a specific list item must include the item's name and position in its accessible label.

## Expected Behavior

- Layouts across the setup wizard stack vertically on narrow/mobile screens and switch to horizontal when the viewport is wide enough.
- Dialogs that display long content render with a fixed maximum height, a scrollable body area, and a footer that always stays visible and does not scroll away.
- The setup page sidebar is hidden on mobile, with a dedicated accessible menu available instead.
- Interactive buttons for removing, configuring, editing, or viewing documentation for a specific item carry labels that identify both the action and the item (e.g., the button to remove the second item in a list clearly names that item and its position).
- When there is only one removable item, its remove button is disabled to prevent accidentally clearing all entries.
- A new component displays estimated run duration and cost in a row that stacks on small screens.

## Why This Matters

These changes make the wizard usable on mobile and tablet devices and ensure compliance with accessibility standards, allowing screen reader users to navigate and interact with the setup flow effectively.
