Implement support for position restraints in the molecular dynamics simulation framework. Create classes and functions to anchor particles to fixed positions using harmonic spring forces, and ensure the system can read these configurations from input files.

*   Implement the `HarmonicRestraintPotential` class in `mjolnir/potential/external/HarmonicRestraintPotential.hpp`:
    *   Make it a template class parameterized on a floating-point type `real_type`.
    *   Include a constructor accepting `std::vector<std::pair<real_type, real_type>>` for (spring constant, equilibrium distance).
    *   Provide a `potential(std::size_t i, real_type r)` method returning `k * (r - r0)^2`.
    *   Provide a `derivative(std::size_t i, real_type r)` method returning `2 * k * (r - r0)`.
    *   Include a `parameters()` method returning a reference to the internal vector of (k, r0) pairs.
    *   Ensure compatibility with both double and float precision types.

*   Implement the `PositionRestraintInteraction` class in `mjolnir/interaction/external/PositionRestraintInteraction.hpp`:
    *   Make it a template class parameterized on `traitsT` and `potentialT`.
    *   Inherit from `ExternalForceInteractionBase<traitsT>`.
    *   Include a constructor accepting `std::vector<std::pair<std::size_t, coordinate_type>>` and a potential object.
    *   Implement `calc_force(system_type&)` to apply forces based on the potential's derivative.
    *   Implement `calc_energy(system_type const&)` to compute energy contributions.

*   Implement `read_harmonic_restraint_potential<realT>` in `mjolnir/input/read_external_potential.hpp`:
    *   Parse a TOML "parameters" array with "index", "k", and "v0" fields.
    *   Return a `HarmonicRestraintPotential<realT>` with correctly indexed parameters.

*   Extend `read_external_interaction<traitsT>` in `mjolnir/input/read_external_interaction.hpp`:
    *   Handle interaction type "PositionRestraint" with potential "Harmonic".
    *   Parse parameter entries with "position", "index", "k", and "v0".
    *   Return a `std::unique_ptr` to `PositionRestraintInteraction<traitsT, HarmonicRestraintPotential<real_type>>`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.