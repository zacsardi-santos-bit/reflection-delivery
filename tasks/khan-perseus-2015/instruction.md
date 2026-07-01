Refactor the NumericInputEditor component to improve accessibility and usability by removing the toggle mechanism and organizing configuration options into clearly labeled groups. Ensure all settings are directly visible and accessible without any toggle interaction.

*   Remove the "Toggle options" control and ensure all settings are always visible.
*   Implement radio button groups with accessible names for mutually exclusive options:
    *   "Width" group with radio buttons:
        *   "Normal (80px)" - Calls `onChange` with the appropriate width value.
        *   "Small (40px)" - Calls `onChange` with the appropriate width value.
    *   "Alignment" group with radio button:
        *   "Right" - Calls `onChange` with `{rightAlign: true}`.
    *   "Number style" group with radio button:
        *   "Coefficient" - Calls `onChange` with `{coefficient: true}`.
    *   "Answer formats are" group with radio button:
        *   "Required" - Calls `onChange` with the value indicating answer formats are strictly required.
    *   "Unsimplified answers are" group with radio buttons:
        *   "Ungraded" - Calls `onChange` with the value indicating unsimplified answers are ungraded.
        *   "Accepted" - Calls `onChange` with the value indicating unsimplified answers are accepted.
        *   "Wrong" - Calls `onChange` with the value indicating unsimplified answers are wrong.
*   Render a textbox with the accessible name "aria label" (all lowercase). Typing into it must call `onChange` with the updated label text.
*   Ensure answer format checkboxes are directly accessible by their names without requiring any toggle interaction:
    *   "Integers"
    *   "Decimals"
    *   "Proper fractions"
    *   "Improper fractions"
    *   "Mixed numbers"
    *   "Numbers with π"

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.