## Description

The library currently provides an offline reinforcement learning algorithm (IQL — Implicit Q-Learning) that works well for continuous action spaces, but there is no corresponding implementation for **discrete action spaces**. Many real-world environments — games, navigation tasks, and other sequential decision-making problems — use a fixed set of categorical actions, so practitioners cannot currently apply this offline RL algorithm to them without significant manual adaptation.

## Expected Behavior

- A new loss module for discrete-action IQL should be available in the objectives package so it can be used like other loss modules.
- The module should jointly train an actor network, a value network, and one or more Q-value networks with three independent, separately backpropagatable loss terms (actor loss, value loss, Q-value loss), plus an entropy measure.
- Backpropagating each individual loss term should only update the parameters of the corresponding network — gradients should not leak across the three network components.
- The module should support configurable temperature and expectile parameters, a configurable number of Q-value networks, and an option to treat shared layers between networks as separate during optimization.
- The module should work with all standard temporal-difference value estimators (TD(0), TD(1), TD(λ)) and raise an appropriate error for unsupported estimators.
- A runnable example training script for discrete IQL should be provided alongside existing examples.

## Why This Matters

Without a discrete-action IQL variant, users working on offline RL for environments with categorical action spaces must either use the continuous-action version (which requires workarounds) or switch to a different algorithm entirely. Adding this variant makes the library consistent and accessible for a much broader range of applications.
