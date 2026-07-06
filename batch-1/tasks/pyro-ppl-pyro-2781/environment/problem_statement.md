## Description

Pyro's funsor-based backend is missing support for MAP (maximum a posteriori) estimation of discrete latent variables. This is a fundamental inference capability — finding the single most probable assignment of all discrete hidden variables given the observed data — analogous to running a Viterbi decoder for hidden Markov models or exact discrete inference in graphical models.

Without this feature, users of the funsor contrib backend have no built-in way to "decode" discrete hidden states. For example, in a hidden Markov model, there is currently no way to infer the most likely sequence of hidden states from observations using the funsor backend.

## Expected Behavior

- There should be a utility that wraps an enumerated model and returns a new callable that, when run, produces the most probable discrete variable assignment given the observations.
- The resulting execution trace's total log probability should match the maximum possible log probability across all discrete configurations.
- Each discrete latent site in the result should contain its MAP value.
- The feature should work across a variety of model structures: single discrete latents, models with sequential dependencies between discrete variables, plate-vectorized discrete latents, and mixed models combining these structures.
- In hybrid inference workflows — where continuous variables are first sampled from a variational posterior or MCMC chain and then held fixed — discrete MAP inference should work correctly and should not introduce spurious dependencies between continuous and discrete sites.

## Why This Matters

This enables Viterbi-style decoding and exact discrete inference for funsor-backend users, and supports hybrid continuous/discrete inference workflows where users want posterior samples of continuous latents combined with MAP estimates of discrete latents.
