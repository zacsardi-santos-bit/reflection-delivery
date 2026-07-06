## Description

The analytics module currently tracks whether audience segmentation has been set up using a simple true/false flag. This approach doesn't capture which specific user performed the setup, which makes it impossible to distinguish between "setup not done" and "setup was done by someone else." We need to change this to record the identity of the user who completed the setup.

## Expected Behavior

- The audience segmentation settings should store the identity of the user who completed the setup, not just whether setup is complete or not.
- The default state (when no setup has occurred) should represent an explicitly "not set" value, distinct from a false boolean.
- A new introductory notification should be displayed to users who have access to the dashboard but did not personally configure audience segmentation. This notification should:
  - Welcome the user and explain that they can compare site visitor groups
  - Include a way for the user to navigate directly to the audience segmentation section of the dashboard
  - Dismiss itself permanently once the user interacts with it
  - Not display at all if the user has already dismissed it

## Why This Matters

When one admin sets up audience segmentation, other admins using the same site may not realize the feature is now active. By tracking who set up the feature and showing a targeted introductory notification only to users who didn't do the setup themselves, we can improve feature discovery without showing redundant messages to the person who configured it.
