Centralize the test rendering utilities and mock declarations in the Mattermost mobile project to reduce redundancy and improve maintainability. Implement shared functions for rendering components with internationalization and Redux store providers, and move the native image picker mock to a global setup.

*   Create a new shared file at `test/testing_library.js` to export the following functions:
    *   `renderWithIntl(component, locale = 'en')`: 
        *   Accepts a React component and an optional locale string.
        *   Wraps the component in an internationalization provider with the given locale.
        *   Returns the result of `@testing-library/react-native`'s render function.
    *   `renderWithRedux(component, store = defaultStore)`:
        *   Accepts a React component and an optional Redux store.
        *   Wraps the component in a Redux store provider.
        *   Returns the result of `@testing-library/react-native`'s render function.
    *   `renderWithReduxIntl(component, store = defaultStore, locale = 'en')`:
        *   Accepts a React component, an optional Redux store, and an optional locale string.
        *   Wraps the component in both a Redux store provider and an internationalization provider.
        *   Returns the result of `@testing-library/react-native`'s render function.

*   Update the global test setup file `test/setup.js`:
    *   Include a mock for the `react-native-image-picker` module.
    *   Specifically mock `launchCamera` as a `jest.fn()`.

*   Ensure the following behaviors for the `PostDraft` component:
    *   In normal state, an element with `testId 'post_input'` must be present, and no 'Close Channel' text should be rendered.
    *   With `channelIsArchived=true` or `deactivatedChannel=true`, no element with `testId 'post_input'` should be present; texts 'You are viewing an ', 'archived channel', '. New messages cannot be posted.', and 'Close Channel' must be rendered.
    *   With `channelIsReadOnly=true` or `canPost=false`, no element with `testId 'post_input'` should be present; the text 'This channel is read-only.' must be rendered, and no 'Close Channel' text should be rendered.

*   Implement the `renderSystemMessage` function to handle different post types:
    *   For a channel header update, display '{username} updated the channel header from: {oldHeader} to: {newHeader}'.
    *   For a channel display name update, display '{username} updated the channel display name from: {oldDisplayName} to: {newDisplayName}'.
    *   For an archived channel post type, display '{username} archived the channel'.
    *   For an unarchived channel post type with a username, display '{username} unarchived the channel'. Without a user, `toJSON()` must return null and the text '{username} archived the channel' must not be found.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.