## Description

The storage browser's "create folder", "create file", and "upload file" actions are currently implemented inline within the directory page component. This tightly coupled approach makes the code harder to maintain and test independently. These actions should be extracted into a dedicated, self-contained component that can be developed and tested on its own.

Additionally, several UI text labels in the storage browser need to be refreshed. The drag-and-drop file upload area currently displays a long, informal message that doesn't match the visual polish expected in the application. Both its default (no drag in progress) and active-drag states need shorter, cleaner labels. The input label on the rename action's modal should also be simplified.

## Expected Behavior

- A new dedicated component consolidates create-folder, create-file, and upload actions under a single "New" button with a dropdown
- The dropdown organizes actions into clearly labeled sections separating creation from upload
- Each action opens an appropriate modal with a descriptive title and relevant confirmation controls
- When a user creates a folder or file, the component submits the entered name together with the current directory path to the appropriate API endpoint
- The drag-and-drop upload area shows concise text in both its idle and active-drag states
- The rename modal uses a shorter, cleaner input label
- The table data type used throughout the storage browser should use a name that reflects it is specific to the directory view

## Why This Matters

Extracting these actions into an independent component improves modularity and makes each piece independently testable. The UI text improvements make the storage browser look and feel more polished and consistent.
