Implement enhancements to the AI tutor suggestion feature in Artemis to improve navigation, error handling, and support for text exercises. Ensure tutors can navigate through multiple suggestions, detect new student answers, and handle errors effectively.

*   Implement navigation logic:
    *   Add a public `switchSuggestion(up: boolean): void` method in `TutorSuggestionComponent`.
        *   Move to the next suggestion if `up` is true, or to the previous if false.
        *   Ensure no operation occurs if `suggestion` or `suggestions` is undefined, or if the current suggestion is not found.
        *   Prevent navigation beyond the first or last suggestion.
    *   Implement a private `updateArrowDisabled(currentIndex: number): void` method.
        *   Set `downDisabled` to true if `currentIndex === 0`.
        *   Set `upDisabled` to true if `currentIndex === suggestions.length - 1`.
        *   Set both to false for middle positions or true for a single-element list.

*   Detect new student answers:
    *   Create a private `checkForNewAnswerAndRequestSuggestion(): boolean` method.
        *   Return false if `post` is undefined, `post.answers` is empty or undefined, `suggestions` is empty, or the last suggestion lacks a `sentAt` property.
        *   Return true if the latest answer's `creationDate` is strictly after the last suggestion's `sentAt`.

*   Enhance error handling:
    *   Implement a private `requestSuggestion(): void` method.
        *   Return early if `irisEnabled` is false or `post` is null/undefined.
        *   On message stream error, set `error` to `IrisErrorMessageKey.SESSION_LOAD_FAILED` and treat messages as empty.
        *   Call `requestTutorSuggestion()` if conditions are met, and handle errors by setting `error` to `IrisErrorMessageKey.SEND_MESSAGE_FAILED`.
    *   Add a public `userRequestedNewSuggestion(): void` method.
        *   Directly call `requestTutorSuggestion` and handle errors by setting `error` to `IrisErrorMessageKey.SEND_MESSAGE_FAILED`.

*   Support text exercises:
    *   Ensure the tutor suggestion pipeline includes text exercise details in the `textExerciseDTO` field when relevant.
    *   For programming exercises, populate the `programmingExerciseDTO` field with appropriate data.

*   Ensure the `TutorSuggestionComponent` exposes necessary properties:
    *   Public properties: `suggestion`, `suggestions`, `upDisabled`, `downDisabled`.
    *   Private properties: `error`, `irisEnabled`, `chatService`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.