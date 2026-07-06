## Description

Tinygrad's in-place tensor mutation (element assignment) can silently corrupt the computation graph when another tensor holds a lazy reference to the tensor being mutated. This currently only raises an error when gradient tracking is enabled, but the same divergence problem exists for all tensors regardless of gradient tracking.

## Problem

When a tensor is mutated in-place:
- If another tensor holds an unrealized downstream computation that depends on the mutated tensor, the mutation updates the underlying buffer but the dependent tensor still holds the old computation graph. Evaluating it later will produce results that diverge from what eager execution would have produced.
- If an unrealized tensor has any live views or slices, mutating the tensor in-place leaves the views referencing stale graph state.
- If two tensor objects share the exact same underlying operation reference (aliased), mutating via one leaves the other with a stale reference.

All three scenarios currently proceed silently without error (unless gradient tracking is enabled), making it easy to write code that appears to work but produces incorrect results.

## Expected Behavior

In-place element assignment should raise an error whenever the mutation would diverge from eager execution semantics — specifically, when any other live tensor object has a reference to the computation of the tensor being mutated. This check should apply universally, not only when gradient tracking is enabled.

## Why This Matters

Silent graph corruption is extremely hard to debug. Failing loudly with a clear error message in these unsafe mutation scenarios is far better than allowing programs to silently produce wrong results. Users relying on the lazy evaluation model need to be protected from accidental aliasing and stale-graph bugs.
