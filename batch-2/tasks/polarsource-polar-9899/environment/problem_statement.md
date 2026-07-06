## Description

When an AI agent reviews an organization — either at initial submission or when payment activity crosses a threshold — it currently has no visibility into prior review decisions. This means the agent may repeatedly flag issues that a human reviewer has already investigated and resolved, creating unnecessary friction and inconsistent outcomes.

We need to collect historical review decisions (both automated and human) from the database and inject them into the agent's review context. This allows the reviewing agent to see what was decided before, who made the decision, what risk assessment was assigned, and any reasoning provided — and to explicitly avoid re-raising concerns that have already been addressed.

## Expected Behavior

- Prior review decisions (both automated and human) for an organization should be fetchable from the database in chronological order, excluding any soft-deleted records.
- Each decision record should be transformable into a structured summary including actor type, decision, review context, the agent's prior risk assessment, violated sections, dimension-level findings, and any reviewer reasoning.
- The agent's review prompt should include a dedicated section summarizing all prior decisions when they exist, with guidance not to re-raise already-resolved concerns. If no prior decisions exist, this section should be omitted entirely.
- The data structures used to persist review snapshots should accommodate prior feedback history in a backward-compatible way, so that existing stored records without this data continue to deserialize correctly.

## Why This Matters

Without this context, the review agent operates in isolation each time, causing redundant denials and eroding trust with legitimate organizations that have already passed manual review. Including prior decision history makes the review process more consistent and reduces unnecessary escalations.
