## Description

The TextInput component, when used with an autocomplete suggestions list, does not include the proper accessibility attributes needed for assistive technologies to interpret the widget correctly. As a result, screen reader users have no indication that the input field is an autocomplete combobox, the suggestions dropdown has no semantic role identifying it as a list of selectable options, and individual suggestion items have no role or selection state.

## Expected Behavior

- When a TextInput has suggestions, the input element should identify itself as an autocomplete combobox and expose whether its suggestion list is currently open or closed.
- The suggestions dropdown container should identify itself as a listbox and carry a unique identifier that the input can reference.
- Each suggestion item in the dropdown should identify itself as a selectable option, carry its own unique identifier, and expose whether it is currently selected.

## Why This Matters

Without these attributes, users relying on screen readers or other assistive technologies receive no meaningful information about the autocomplete behavior. They cannot tell that suggestions are available, cannot navigate to the option list, and cannot identify individual options as they move through the list. Adding the appropriate roles, states, and identifiers makes the component comply with established accessibility patterns for combobox widgets, ensuring the experience is usable for all users.
