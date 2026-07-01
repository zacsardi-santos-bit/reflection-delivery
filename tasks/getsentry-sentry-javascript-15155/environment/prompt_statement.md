I'm working on improving how schema validation errors get reported to Sentry. There are two issues I'd like to fix.

First, when a validation failure occurs inside an array, the error message currently doesn't make it clear that the failure is at an array index. For example, a path like "names" followed by a numeric index currently shows up as just "names" in the Sentry error message, losing the information that an array element was involved. I'd like numeric array indices in paths to be replaced with a standardized placeholder text so the message clearly indicates that an array element was involved.

Second, when there are many validation issues at once, only a limited number can fit in the Sentry event's extras, and the rest are lost. I'd like to add support for attaching the full list of issues as a separate file when there are more issues than the limit allows. The attachment should be a JSON file containing all the issues, and the limit should still apply to the inline extras. A new boolean option should control whether the attachment behavior is enabled.

I also want to expose the individual helper utilities — for flattening a path array to a string, for flattening a full issue object, and for generating the formatted message from a validation error — as public exports so they can be tested and reused independently.
