## Description

When a selectbox uses custom objects as options and a display function that relies on object identity (for example, looking up objects in a dictionary built from the current run's instances), the user's selection gets silently reset to the first option on every rerun after the initial selection. This happens because each rerun creates fresh object instances, making the previously stored selection a "stale" object that the new lookup fails to recognize. Instead of preserving the selection, the widget currently falls back to the default whenever the display function errors on the stale value.

The same problem affects selectboxes using enumerated types when the automatic enum-coercion step is disabled: the serialized label of the selection is discarded during coercion, so the fallback mechanism cannot recover the user's intended selection either.

## Expected Behavior

- When the display function throws an error for the stored (stale) selection but the selection still logically corresponds to a current option, the widget should preserve the user's choice rather than resetting it.
- A wire-level label fallback should be used: if the display function fails, the widget should look up the serialized label of the stored value and map it to the matching current option.
- When enum coercion is applied, the serialized label must be carried through so the fallback remains available.
- A new utility function should be provided to encapsulate this resolution logic, handling all cases: no value, value found normally, display function failure with a recoverable wire label, and display function failure with no recoverable label (reset to default).

## Why This Matters

Apps that define option classes inline (inside the script body) or use dictionary-keyed display functions are currently broken when users try to interact with a selectbox — any selection made by the user is immediately undone on the next rerun, making the widget appear to ignore input.
