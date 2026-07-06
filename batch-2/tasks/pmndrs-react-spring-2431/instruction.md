I've been running the animation library in a project with strict development mode enabled, and I'm hitting some strange animation behavior that only happens in development.

*   The spring stop operation must only snap the animation goal to the current value when a goal target (`to`) already exists on the animation. If the spring's `animation.to` is undefined — meaning the spring was initialized from a `from` value but never given an animation goal — calling stop must leave the goal as undefined rather than establishing one.

*   When `SpringContext` is rendered in React.StrictMode (which double-invokes layout effects on initial mount), the sequence of context updates during that initial mount must be exactly: (1) the user-defined animation update containing the `onProps` callback and `to` target, (2) a default context broadcast containing `{ pause: false, immediate: false }`, (3) the user-defined animation update again. This reflects the mount → simulated unmount → remount cycle where the second pass re-broadcasts defaults because `defaultProps.onProps` was set by the first pass.

*   When `useSprings` is used with a props function under React.StrictMode, the props function must be invoked twice per initial render — consistent with a multiplier of 2 — because StrictMode double-invokes effects.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.