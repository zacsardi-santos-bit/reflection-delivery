## Description

When a resource name (such as a bucket or volume name) is too short, the validation logic can return a misleading error message. If the too-short name also happens to consist entirely of digits, the current code detects the all-numeric pattern before checking the length, and the user receives an error message about the name being all-numeric — when the actual problem is simply that the name is too short.

## Expected Behavior

- A name that is below the minimum allowed length should always produce an error that clearly identifies the length as the problem, regardless of whether the name is also all-numeric.
- The error message for any name whose length falls outside the allowed range should indicate that the length is invalid.
- Length validation should take priority over content-based validation rules.

## Current Behavior

A short all-numeric string like "12" fails validation with an error about the name being all-numeric rather than about the name being too short. This is confusing because the length violation is the more fundamental issue and should be reported first.

## Why This Matters

Users who accidentally create a resource with a name that is too short and happens to be numeric receive a misleading error message that directs them to fix the wrong problem. Consistently checking length before content patterns makes the error message accurate and actionable.
