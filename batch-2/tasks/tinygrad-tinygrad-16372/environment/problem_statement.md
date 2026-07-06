## Description

Tensor factories that fill with a constant value (all-ones, all-zeros, or a scalar fill) currently produce "virtual" broadcast constants rather than real memory-backed tensors. This causes a surprising inconsistency: calling the standard "realize" operation on such a tensor does **not** result in a genuinely realized, memory-backed tensor — even though the same operation on any other kind of tensor does. Code that expects a realized constant tensor to behave like any other realized tensor will encounter unexpected behavior.

## Expected Behavior

- After calling the built-in realize function on a constant-fill tensor (e.g. ones or zeros), the resulting tensor should be backed by actual allocated memory, and its "is realized" status should be True — consistent with all other realized tensors.
- An explicit option should be available on constant-fill tensor factories for callers that specifically want the lightweight broadcast-constant behavior (no allocation). With this option enabled, the tensor should require no schedule operations to access constant elements.
- Existing code that relied on constant-fill tensors being broadcast constants should continue to work when that option is explicitly passed.

## Why This Matters

Users who realize a ones or zeros tensor and then treat it as a concrete realized buffer get inconsistent behavior compared to every other tensor type. Making the default match the general contract (realized = has a buffer) removes a footgun and allows constant tensors to participate uniformly in operations that require realized inputs. The opt-out option preserves the old lightweight behavior when explicitly requested.
