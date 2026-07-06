## Description

In our mobile hybrid app environment, there is a category of users who have been administratively locked into the new app experience and must not be able to return to the old app. However, this lock is not currently enforced at the UI and action level: locked users can still see entry points to redirect to the old app, trigger the confirmation modal for that redirect, and exit the new app experience entirely. We need to enforce this lock so that locked users are prevented from navigating away in the mobile context.

## Expected Behavior

- When a user is mobile-locked in the HybridApp environment:
  - All old-app exit actions must be blocked — the native close must not be triggered, regardless of the type of exit requested
  - The GPS handoff modal leading to the old app must also be suppressed
  - UI entry points (such as the FAB redirect option) must become inactive and must not open a confirmation dialog when triggered
  - The old-app navigation confirmation modal must not render at all
- The mobile lock must not affect the same users when accessing via web — classic redirect gating on web should remain unaffected
- The lock state must be re-evaluated correctly after session changes: switching accounts should cause the system to wait for the new user's lock state before allowing any exits; token rotation for the same account must not disrupt an already-resolved unlocked state
- If the lock state is cleared and then the app reloads, the system must re-evaluate the lock before allowing exits again

## Why This Matters

Users who are locked to the new app experience are expected to remain there — allowing them to navigate back to the old app defeats the purpose of the lock and can lead to inconsistent state or broken workflows tied to the new app.
