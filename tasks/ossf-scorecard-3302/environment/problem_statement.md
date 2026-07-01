## Description

The scorecard project needs two new code review probes to provide more granular assessment of whether a project's default branch changes are being properly reviewed before merge. Currently, the code review check operates at a higher level, but there is no structured probe-level mechanism to separately evaluate two distinct aspects of review quality:

1. Whether every human-authored changeset was **formally approved** by at least one person who is not the author of that change.
2. Whether every human-authored changeset was **reviewed** (even without a formal approval) by at least one person who is not the author.

## Expected Behavior

- Both probes should analyze the set of recent changesets on the default branch.
- Changesets authored by bots should be excluded from the evaluation — they should not contribute to a pass or fail outcome.
- If the author data for a changeset cannot be retrieved (empty login), the probe should report that the result is not available rather than producing a false positive or negative.
- If reviewer data is missing (empty reviewer login), the probe should similarly report unavailability.
- Self-reviews — where the author reviews their own change — must not count toward satisfying the review requirement.
- Duplicate reviews from the same reviewer should be deduplicated; only unique reviewers count.
- The "approval" probe must require a formal approval state from a non-author; a non-approval review does not satisfy it.
- The "reviewer count" probe only requires that at least one different person reviewed the change, regardless of approval state.

## Why This Matters

Breaking the code review assessment into two separate probes allows consumers of scorecard data to distinguish between projects that have any peer review and those that go further by requiring formal approvals. This also makes it easier to compose these probes into higher-level scoring logic with different thresholds and weights.
