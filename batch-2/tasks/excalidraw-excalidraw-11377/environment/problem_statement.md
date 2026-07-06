## Description

There are two related bugs with animation management and collaborative laser pointer cleanup.

**Animation system bug:** The internal animation management system has a flaw where starting a new animation after the previous one was cancelled does not work correctly. Additionally, when animations are cancelled or finish naturally, orphaned timers can remain active in the background — even after all animations have stopped. This wastes resources and can cause unexpected behavior.

**Laser trail persistence bug:** When all remote collaborators leave a shared drawing session, any laser pointer trails they were drawing are not cleaned up from the canvas. The visual artifacts (trail paths) remain visible even though the collaborators have disconnected, which is confusing and incorrect.

## Expected Behavior

- After an animation is cancelled, starting a new animation should work reliably.
- When all animations have ended or been cancelled, no timers should remain scheduled.
- When all remote collaborators leave the session, their laser pointer trails should be removed from the canvas immediately.

## Why This Matters

Leaving orphaned timers after animations complete wastes resources and could cause subtle rendering glitches. Leaving laser trails on screen after collaborators have left is a visual correctness issue that gives users incorrect information about who is currently active in the session.
