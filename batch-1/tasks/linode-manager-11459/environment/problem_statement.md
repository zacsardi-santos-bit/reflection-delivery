## Description

The Profile Settings page is currently implemented as a large, monolithic component that handles all user preferences together in one place. This makes it difficult to maintain, test, and reason about individual settings in isolation. We want to refactor this page by extracting each distinct settings section into its own self-contained component — one for theme selection, one for email notifications, one for masking sensitive data, and one for the type-to-confirm feature.

Additionally, the preference-fetching hook used by components should support selecting only the specific preference value a component needs, rather than always returning and destructuring the entire preferences object. This avoids unnecessary coupling between individual components and the full preferences data shape.

## Expected Behavior

- A dedicated component for the "Mask Sensitive Data" toggle that reads its own preference, shows the toggle state, and displays descriptive text indicating whether data is currently masked or visible.
- A dedicated component for email notification settings that reads its own profile data, shows the toggle state, and displays text indicating whether email alerts are enabled or disabled.
- A dedicated component for theme selection that reads the stored theme preference and reflects the selected theme (System, Light, or Dark), defaulting to System when no preference is stored.
- A dedicated component for the type-to-confirm setting that reads its own preference, shows the toggle state (defaulting to enabled when no preference is set), and displays text indicating the current state.
- The preference-fetching hook, when used with a selector, returns the selected value directly so components receive only the data they need.

## Why This Matters

Breaking the settings page into focused components makes each section independently testable and reduces the risk of changes in one setting accidentally affecting another. It also makes the codebase easier to navigate and understand.
