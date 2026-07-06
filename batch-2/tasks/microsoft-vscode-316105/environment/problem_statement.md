## Description

The Copilot chat quota notification system currently shows threshold warnings immediately on startup if usage already exceeds a threshold — even when no new threshold was crossed during the current session. For example, if a user is already at 80% usage when they open VS Code, they immediately see a "Credits at 75%" warning the moment quota data loads. This is surprising and potentially annoying, since the threshold was not crossed during this session.

## Expected Behavior

- When the first quota data arrives after sign-in or after VS Code loads, the system should silently establish a usage baseline without showing any warnings.
- Threshold notifications should only appear when usage actively increases past a threshold boundary during a session — not for thresholds that were already exceeded before the session started.
- After signing out, the baseline should be cleared so that signing back in re-establishes a fresh baseline (again, without showing notifications for already-crossed thresholds).
- The quota service needs the ability to refresh quota data on demand, and this capability should be exposed as part of the service's public interface.

## Why This Matters

Users are seeing threshold warnings immediately on startup that don't represent any action they took in the current session. The notifications should feel meaningful — they should reflect actual usage increases, not just the state of usage when VS Code happened to start. This makes the quota warning system more signal-rich and less noisy.
