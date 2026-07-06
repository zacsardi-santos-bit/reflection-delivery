## Description

Students who have submitted multiple attempts for a modeling exercise currently have no way to review their submission history and see the feedback associated with each individual attempt. When a student navigates to their modeling exercise, they can only see their latest submission, with no way to go back and view what feedback was provided for an earlier submission.

Additionally, the system that provides automated AI feedback for modeling exercises currently represents element references as a list of identifiers, but Artemis internally uses a single combined reference string (containing both element type and identifier). This mismatch means that feedback is not always properly associated with the correct diagram element.

## Expected Behavior

- Students should be able to navigate to a specific past submission and view the feedback results associated with it in a dedicated "feedback view" mode.
- When in feedback view mode, the component should load and display a sorted history of all submissions with their results, sorted by the most recent result completion date.
- Each entry in the result history should correspond to the latest result from its submission, and should carry along the participation context.
- Before the assessment deadline, students in feedback view mode should only see automated AI-generated feedback; after the deadline passes, all feedback including manual instructor assessments becomes visible.
- The automated AI feedback service should represent element references as a single combined string rather than an array, so that feedback can be reliably linked to specific diagram elements.
- When an automated AI feedback generation fails, the student should receive an error notification and the "generating feedback" state should be cleared. When it succeeds, a success notification should be shown instead.

## Why This Matters

Without submission history navigation, students lose the ability to understand how their thinking evolved across attempts and what feedback was given for earlier work. The element reference format issue also means that AI-generated feedback may not correctly highlight the corresponding parts of the diagram, reducing the usefulness of the feedback.
