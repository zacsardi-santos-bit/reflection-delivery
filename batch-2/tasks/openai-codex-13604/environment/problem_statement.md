## Description

When users are about to submit feedback, the consent popup currently only indicates that connectivity diagnostic information *will be attached* — it does not show users what that information actually contains. This means users must agree to share data without being able to see it first, which is a transparency gap.

Additionally, the feedback note entry screen currently previews the connectivity diagnostics (such as environment variable settings that may affect network connectivity), but this is the wrong place for that information. Users haven't been asked to consent yet at that point, and they see the diagnostics before deciding whether to share.

## Expected Behavior

- The feedback consent popup should display the full connectivity diagnostics inline — each diagnostic headline and its detail lines — so users can read exactly what will be shared before deciding.
- The feedback note entry view should be simplified to no longer show a preview of the connectivity diagnostics; that information belongs in the consent popup.
- The consent popup should continue to list the connectivity diagnostics attachment in the set of files to be uploaded, but only when there are diagnostics to include.

## Why This Matters

Users deserve to see exactly what environment data (proxy settings, base URL overrides, etc.) will be uploaded when they submit feedback. Surfacing this information at the consent step — rather than in a prior screen — gives them the transparency they need to make an informed decision.
