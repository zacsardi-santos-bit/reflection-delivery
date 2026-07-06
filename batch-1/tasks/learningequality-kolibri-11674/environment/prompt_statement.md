I'm working on updating the quiz/exam feature in our learning platform to support a new data format where questions are organized into sections rather than stored as a flat list. Each section needs its own identifier, resource pool, question count, and an optional list of questions.

The API should validate that new exams are created using this section-based structure, and reject submissions that still use the old flat question format. However, quizzes already saved in the old format should still be retrievable without breaking — we need backward compatibility on reads even as we enforce the new format on writes.

I also need the exam model to expose a method that returns all questions as a flat list regardless of which version of the data format the exam uses. For the new format this means flattening questions out of their sections; for the older formats it just returns the questions directly.

Finally, the internal system that tracks which content needs to be downloaded for an exam needs to be updated to look inside the sections for question identifiers, while still handling the legacy flat format correctly.
