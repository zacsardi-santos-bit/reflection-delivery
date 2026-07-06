## Description

The symbolic simplification engine currently handles integer division nesting: when a scaled variable plus a small offset is divided by a larger multiple of the scale factor, the engine recognizes that the low-order term can be dropped and simplifies accordingly. However, the complementary rule for modulo is missing. The analogous modulo pattern — taking a scaled variable plus a bounded offset modulo a larger multiple of the scale — is not simplified even when the offset is provably smaller than the scale factor.

This gap affects index arithmetic generated for tensor reshape and loop tiling operations, which frequently produce exactly this modulo pattern.

## Expected Behavior

- The symbolic simplifier should recognize and apply the modulo counterpart of the division nesting rule: a scaled variable plus a small bounded offset, taken modulo a larger multiple of the scale, should reduce to the bounded offset plus the variable taken modulo the ratio of the divisor and scale, multiplied by the scale.
- Once this modulo rule fires, the existing recombination identity — which recognizes that a value equals its integer quotient times divisor plus its remainder — should be able to fire on a broader class of inputs, enabling further end-to-end reductions.
- Real-world index expressions from neural network models should simplify to cleaner forms, and in some cases validity conditions on image loads should collapse away entirely, becoming unconditional accesses.

## Why This Matters

Without this rule, tinygrad's index simplifier leaves avoidable intermediate forms in generated compute kernels. Complex validity checks and multi-layer modulo expressions persist even when the algebra guarantees they are unnecessary. Adding the modulo-nesting simplification produces cleaner kernel indices, removes redundant bounds checks, and mirrors the completeness that the division side already has.
