## Description

The simulation framework currently lacks support for position restraints — a common technique in molecular dynamics where specific particles are tethered to fixed anchor points in space via a harmonic spring force. Without this feature, users cannot perform restrained energy minimizations, gradual system equilibration, or other workflows that require holding particles near reference positions.

## Expected Behavior

- Users should be able to specify a set of particles and assign each to a fixed 3D anchor position.
- Each particle should experience a restoring force proportional to its displacement from the anchor, with a configurable spring constant and equilibrium distance.
- When a particle is closer to its anchor than the equilibrium distance, the force should push it away; when farther, the force should pull it back.
- The system should be able to read position restraint configurations directly from input files, specifying per-particle parameters (spring constant and equilibrium distance) alongside the anchor position.
- Both single and double floating-point precision should be supported.

## Why This Matters

Position restraints are widely used in biomolecular simulations to stabilize structures during preparation, equilibration, and perturbation workflows. Without them, users must implement workarounds or use a different simulation tool for these stages. Adding this capability makes the framework suitable for a wider range of simulation protocols.
