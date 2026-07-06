Implement network-aware hard fork activation logic in the blockchain node codebase to support independent schedules for Mainnet and Floonet. Ensure that the header version validation logic correctly applies the appropriate hard fork schedule based on the active network type.

*   Update the `valid_header_version` function in `core/src/consensus.rs`:
    *   Make it aware of the active chain type (Mainnet or Floonet) using the `global::is_floonet()` function.
    *   On Mainnet:
        *   Ensure header version 1 is valid for block heights 0 through `YEAR_HEIGHT/2 - 1` (inclusive).
        *   Ensure header version 2 is valid starting at block height `YEAR_HEIGHT/2` (inclusive).
        *   Ensure header version 3 is not valid at block height `YEAR_HEIGHT`.
    *   On Floonet:
        *   Ensure header version 1 is valid for block heights 0 through `FLOONET_FIRST_HARD_FORK - 1` (inclusive).
        *   Ensure header version 2 is valid starting at block height `FLOONET_FIRST_HARD_FORK` (inclusive).
        *   Ensure header version 3 is not valid at block height `YEAR_HEIGHT`.
    *   For both Mainnet and Floonet:
        *   Ensure header version 2 is invalid at block heights `YEAR_HEIGHT`, `YEAR_HEIGHT + 1`, and `YEAR_HEIGHT * 3 / 2`.

*   Define and export a new constant `FLOONET_FIRST_HARD_FORK` in `core/src/consensus.rs`:
    *   Set its value to be less than `YEAR_HEIGHT / 2`.
    *   Ensure it is publicly accessible for use in tests and other modules.

*   Ensure the `global::is_floonet()` function in `core/src/global.rs`:
    *   Returns `false` when the chain type is Mainnet.
    *   Returns `true` when the chain type is Floonet.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.