## Description

The AI tutor suggestion feature in Artemis needs several improvements to be more useful and robust for tutors.

Currently, the suggestion panel displays only a single AI-generated suggestion with no way to navigate between older and newer ones. When multiple suggestions have been generated over the course of a session, tutors cannot go back to review a previous suggestion or advance to a newer one. Navigation arrows exist in the UI but the logic behind them is incomplete.

In addition, the system does not currently detect when a student has added a new answer to the discussion thread after the last suggestion was generated. Ideally, if a student posts a new reply after the AI provided a suggestion, the system should automatically detect this and request a fresh, updated suggestion that accounts for the new content.

Error handling is also insufficient: if the service fails while loading the session history, the failure is silent and the tutor receives no indication that something went wrong. Similarly, if the suggestion generation itself fails, the error is not surfaced. Both failure modes should update the visible error state so the tutor knows what happened.

Finally, the feature currently only works for programming exercises. Tutors helping students in text exercise discussion channels should also benefit from AI suggestions.

## Expected Behavior

- Tutors can navigate forward and backward through the list of AI suggestions using arrow buttons.
- Navigation arrows are disabled when the tutor has reached the first or last suggestion in the list.
- When a student adds a new answer after the last AI suggestion was created, the component automatically requests a new suggestion.
- If loading the session message history fails, the error is recorded and the suggestion request still proceeds.
- If the suggestion generation request fails, the error is recorded and visible.
- Tutors in text exercise channels receive AI suggestions, with the text exercise's details sent as part of the pipeline request.

## Why This Matters

These improvements make the tutor suggestion feature more reliable, informative, and broadly applicable across exercise types, improving the experience for tutors responding to student questions.
