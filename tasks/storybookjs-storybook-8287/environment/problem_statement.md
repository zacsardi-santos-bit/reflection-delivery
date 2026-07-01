## Description

There are two related bugs in Storybook's knobs addon and hooks system that appear when decorators call the story function more than once.

**Bug 1 — Knob options not updated on re-render**

When a story renders and a knob already exists in the store with the same type, any extra configuration options provided to the knob (beyond its value) are silently ignored. This means that if the story changes knob configuration — such as updating labels or choices — on subsequent renders, those changes are never applied. The knobs panel continues to show the stale configuration from when the knob was first registered.

**Bug 2 — Side effects fire multiple times when a decorator calls the story function more than once**

Some decorator patterns intentionally call the story function twice in a single render cycle (for example, to double-render or inspect the story). When this happens, any side effects registered inside the story fire once for each invocation rather than once per render cycle. This leads to duplicate network requests, repeated state updates, or other unwanted behavior.

## Expected Behavior

- When a knob's type has not changed between renders, any updated configuration options on the knob should be applied to the existing knob entry so the panel stays up to date.
- Side effects registered within a story should execute exactly once per render cycle, regardless of how many times a decorator invokes the story function.

## Why This Matters

These bugs combine to make certain decorator patterns unreliable and cause unexpected duplication of work. Fixing them makes it safe to use decorators that call the story function multiple times, and ensures knob configuration stays fresh across re-renders.
