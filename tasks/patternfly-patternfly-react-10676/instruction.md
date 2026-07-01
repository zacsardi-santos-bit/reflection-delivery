Implement new template components and update an existing one in the PatternFly React templates package. Create a `SimpleDropdown` component, a `SimpleSelect` component, and modify the existing `CheckboxSelect` component to enhance functionality and customization.

*   Implement `SimpleDropdown` component in `packages/react-templates/src/components/Dropdown/SimpleDropdown.tsx`.
    *   Render the toggle button as enabled by default; disable it when `isDisabled` is true.
    *   Apply the `toggleVariant` CSS class to the toggle button based on the `toggleVariant` prop.
    *   Set the toggle button's width using the `toggleWidth` prop as an inline style.
    *   Spread properties from `toggleProps` onto the toggle button element.
    *   Use `toggleAriaLabel` to set an accessible label for the toggle button.
    *   Trigger `onToggle` callback with `true` when the toggle button is clicked to open the dropdown.
    *   Trigger `onSelect` callback once when a dropdown item is clicked.
    *   Apply full-width modifier class to the toggle button when `isToggleFullWidth` is true.
    *   Focus the toggle button after an item is selected if `shouldFocusToggleOnSelect` is true.
    *   Render items from `initialItems` array, supporting dividers, links, disabled items, and additional props.

*   Implement `SimpleSelect` component in `packages/react-templates/src/components/Select/SimpleSelect.tsx`.
    *   Render toggle button with "Select a value" when no `toggleContent` is provided.
    *   Set default toggle button width to 200px; allow customization via `toggleWidth`.
    *   Display options in a listbox when the toggle is clicked; remove listbox on toggle click again.
    *   Trigger `onSelect` callback with event and selected option value when an option is clicked.
    *   Trigger `onToggle` callback with `true` when menu opens and `false` when it closes.
    *   Disable toggle button and prevent menu opening when `isDisabled` is true.
    *   Pass `isDisabled` and other props to individual option elements.

*   Update `CheckboxSelect` component in `packages/react-templates/src/components/Select/CheckboxSelect.tsx`.
    *   Apply `toggleWidth` as an inline style to the toggle button; default to 200px if not provided.
    *   Spread properties from `toggleProps` onto the toggle button element.
    *   Ensure the Select wrapper element does not have an `id="checkbox-select"` attribute.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.