I'm working on the automation editor in our publishing platform.

*   When a user clicks 'Publish changes' on an active automation that has staged local edits, the editor must NOT immediately call the edit mutation. Instead it must show a confirmation dialog before proceeding.

*   The confirmation dialog must have the accessible role 'alertdialog' and the accessible name 'Update automation?'.

*   The dialog body must include text that mentions how the update will affect new runs of the automation as well as actively-running ones.

*   After the user confirms inside the dialog by clicking 'Publish changes', the edit mutation must be called with an object containing the automation id, status set to 'active', and arrays for actions and edges.

*   While the edit mutation is in flight after the user confirms, the confirmation button inside the dialog must change its label to 'Publishing...', become disabled, and render a child element with the CSS class 'animate-spin'.

*   If the edit mutation fails (the onError callback fires), the confirmation dialog must remain open and replace the confirmation button with a 'Retry' button that is not disabled and has the CSS class 'bg-destructive'.

*   The existing 'adds three steps locally and publishes them all in the mutate payload' flow for an active automation must route through the confirmation dialog (alertdialog named 'Update automation?') before the mutation fires — clicking the top-level 'Publish changes' button alone must no longer trigger the mutation directly.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.