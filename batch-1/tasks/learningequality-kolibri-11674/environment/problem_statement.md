## Description

The quiz/exam system needs to support a new data model where questions are organized into named sections rather than stored as a single flat list. Currently, all questions in an exam live at the top level of the question sources field, but the new design groups questions under sections — each section has its own identifier, title, description, question pool, and question count.

The API should validate that newly created exams follow this section-based structure and reject any attempt to create an exam using the old flat format. At the same time, exams that were already saved in the old format must still be retrievable without errors, so backward compatibility with older stored data is required.

## Expected Behavior

- Creating a new exam requires question sources to be structured as sections, each with a unique identifier, a resource pool, and a question count
- Sections may optionally contain a list of questions; a section with no questions is valid
- Sections missing required fields (unique identifier, resource pool, or question count) are rejected with a 400 error
- Sections with an invalid (non-UUID) identifier are rejected with a 400 error
- Questions missing their content identifier are rejected with a 400 error
- Attempting to create an exam using the old flat question list format is rejected with a 400 error
- Fetching an older exam stored in the flat format must return successfully
- The exam model must provide a method to retrieve all questions as a flat list, regardless of whether the exam uses the old or new format

## Why This Matters

This change enables richer quiz organization by grouping questions into logical sections. It also ensures that the content download system correctly identifies all required content nodes by traversing the nested section-and-question hierarchy, and that legacy data remains accessible without manual migration.
