I'm working on a bug in the Ghost Admin email settings UI.

*   When a color picker is opened inside the EmailDesignModal (e.g. via ButtonColorField), pressing the Escape key must close only the color picker and must NOT call the modal's onClose callback or remove the modal from the DOM.

*   This Escape key interception must work both when the color picker popover content has fully mounted AND when Escape is pressed immediately after opening the color picker (before the popover content has finished rendering/mounting).

*   The EmailDesignModal must remain visible in the DOM (identifiable by its testId) after Escape is pressed while a color picker is open or in the process of opening.

*   The ButtonColorField component must render a clickable element labeled 'Button color' that, when clicked, opens a color picker popover.

*   The EmailDesignProvider must accept accentColor (string), settings (EmailDesign), and onSettingsChange (function) props and provide context to child components including EmailDesignModal and ButtonColorField.

*   DEFAULT_EMAIL_DESIGN must be exported from the types module as the default email design settings object compatible with EmailDesignProvider's settings prop.


*   Interface details: Type: Component
Name: EmailDesignModal
Location: apps/admin-x-settings/src/components/settings/email-design/email-design-modal.tsx
Description: A modal component for customizing email design settings. Accepts props: open (boolean), preview (ReactNode), sidebar (ReactNode), testId (string), title (string), onClose (function), onSave (function). The modal should remain open when Escape is pressed while a color picker within the sidebar is open.

Type: Component
Name: ButtonColorField
Location: apps/admin-x-settings/src/components/settings/email-design/design-fields/button-color-field.tsx
Description: A design field component that renders a "Button color" label and opens a color picker popover when clicked. When the color picker is open (or in the process of opening), pressing Escape should close only the color picker — it must NOT propagate the Escape key event in a way that closes the parent EmailDesignModal.

Type: Component
Name: EmailDesignProvider
Location: apps/admin-x-settings/src/components/settings/email-design/email-design-context.tsx
Description: A context provider for email design state. Accepts props: accentColor (string), settings (EmailDesign), onSettingsChange (function). Wraps EmailDesignModal and its field components.

Type: Constant
Name: DEFAULT_EMAIL_DESIGN
Location: apps/admin-x-settings/src/components/settings/email-design/types.ts
Description: The default EmailDesign settings object exported from the types module. Used to initialize the EmailDesignProvider in tests.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.