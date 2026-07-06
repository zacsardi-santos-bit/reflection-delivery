## Description

When using Airflow's AI operator integrations to generate text or SQL, there is currently no way to pause a task and route the generated output to a human reviewer before the workflow continues. All LLM-generated content flows directly to downstream steps without any opportunity for human oversight, correction, or rejection.

We need a human-in-the-loop approval capability so that operators can be configured to suspend after generating output and wait for an explicit human decision — approve, modify, or reject — before the task completes.

## Expected Behavior

- Operators should support optional configuration to require human approval of generated output before returning.
- When approval is required, the task should pause and present the generated output to a reviewer with a configurable subject and body.
- Reviewers should be able to approve the output as-is, or optionally edit it (when modification is enabled).
- If a reviewer rejects the output, the task should fail with a clear message identifying who rejected it.
- If the reviewer takes too long to respond, the task should fail with a timeout error.
- For SQL-generating operators, the generated SQL must be validated for safety before being sent for human review. If a reviewer edits the SQL, the modified version must also be re-validated before being accepted.
- Structured data model outputs should be automatically serialized before being presented for review.

## Why This Matters

Many AI-assisted workflows require a human quality gate before their output is acted upon — especially when generating executable SQL queries or producing content that goes directly to end users. Without a built-in approval step, teams are forced to build ad-hoc review mechanisms outside of Airflow, losing auditability and workflow integration.
