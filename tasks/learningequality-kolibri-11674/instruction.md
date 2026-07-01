Update the quiz/exam system to support a new data model where questions are organized into sections rather than a flat list. Ensure new exams are created using this section-based structure while maintaining backward compatibility for older exams. Implement methods to retrieve questions as a flat list and update content assignment logic accordingly.

*   Implement the exam creation API to:
    *   Accept `question_sources` as a list of section objects.
    *   Require each section to have:
        *   `section_id` (hex-encoded UUID).
        *   `resource_pool` (list).
        *   `question_count` (integer).
    *   Optionally include in each section:
        *   `questions` (list of question objects).
        *   `section_title` (string).
        *   `description` (string).
        *   `learners_see_fixed_order` (boolean, default false).
    *   Return HTTP 400 if:
        *   `section_id` is missing or not a valid hex-encoded UUID.
        *   `resource_pool` is missing.
        *   `question_count` is missing.
        *   A question within a section is missing `exercise_id`.
        *   `question_sources` is provided as a flat list of question objects.
    *   Accept sections with no `questions` field and return HTTP 201.

*   Ensure the GET endpoint for an exam:
    *   Returns HTTP 200 for exams stored in the older flat format.

*   Update the Exam model in `kolibri/core/exams/models.py`:
    *   Implement `get_questions(self) -> list` to:
        *   Return a flat list of all question objects.
        *   For `data_model_version` 3, iterate over each section and collect items from each section's `questions` list.
        *   For other versions, return `question_sources` items directly.

*   Update the content assignment logic:
    *   Implement `exam_assignment_lookup(question_sources) -> generator` to:
        *   Yield `(contentnode_id, metadata)` tuples.
        *   For v3-format sections, iterate over `questions` and yield `(question["exercise_id"], None)`.
        *   For legacy flat questions, yield `(item["exercise_id"], None)` directly.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.