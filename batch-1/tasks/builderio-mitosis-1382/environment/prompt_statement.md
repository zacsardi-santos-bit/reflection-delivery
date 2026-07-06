I'm working with a cross-framework component compiler that takes component definitions and outputs code for various target frameworks, including SolidJS. I've noticed that the generated SolidJS code doesn't handle reactivity correctly in two related situations.

First, when a component has computed or derived state — the kind that reads from other state values and derives a result — the compiler emits these as plain functions. But in SolidJS, these derived values need to be wrapped in a memoized reactive computation so the runtime can properly track their dependencies and notify subscribers when the value changes.

Second, when a component specifies a reactive effect that should only re-run when certain values change, the dependency expressions are handed directly to the effect tracking call as raw expressions. SolidJS expects these dependencies to be stable memoized computations rather than raw expressions. Without this, the reactivity tracking for those effects may not work correctly.

Both issues also mean the memoization primitive isn't included in the SolidJS import list, even though it's now required for both of these use cases.

Can you fix the SolidJS generator so that computed/derived state values are emitted as memoized reactive computations, and so that each dependency in a reactive update hook is extracted into an intermediate memoized variable before the hook handler, with the effect referencing those memoized variables instead of the original expressions? The memoization primitive should also be added to the SolidJS imports wherever it's needed.
