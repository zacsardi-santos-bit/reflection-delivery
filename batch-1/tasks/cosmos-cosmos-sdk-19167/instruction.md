Implement a new governance parameter called "yes quorum" to ensure a minimum fraction of total voting power must vote yes for a proposal to pass. Integrate this parameter into both global and per-message governance parameters, and handle validation, migration, and simulation appropriately.

*   Update the governance Params struct:
    *   Add a YesQuorum string field with protobuf field number 20 in `x/gov/types/v1/gov.pb.go`.
    *   Initialize YesQuorum to '0' in the DefaultParams() function.
    *   Define DefaultYesQuorum as `sdkmath.LegacyNewDecWithPrec(0, 1)` in `x/gov/types/v1/params.go`.

*   Implement validation for YesQuorum:
    *   In Params.ValidateBasic(), ensure YesQuorum is non-negative and does not exceed 1.
        *   Return error "yes_quorum cannot be negative: <value>" for negative values.
        *   Return error "yes_quorum too large: <value>" for values greater than 1.
    *   In MessageBasedParams.ValidateBasic(), apply the same validation rules.

*   Modify tally logic in `x/gov/keeper/tally.go`:
    *   Enforce YesQuorum check after the regular quorum check but before the veto threshold check.
    *   If YesQuorum > 0 and yes votes fraction is less than YesQuorum, set proposal pass=false and burnDeposits=false.
    *   Allow per-message params to override yesQuorum for standard proposals.

*   Update migration logic in `x/gov/migrations/v6/store.go`:
    *   Implement MigrateStore to set YesQuorum to default if empty.
    *   Ensure ProposalCancelMaxPeriod, OptimisticAuthorizedAddresses, and OptimisticRejectedThreshold are set from DefaultParams() if missing.

*   Enhance simulation in `x/gov/simulation/genesis.go`:
    *   In RandomizedGenState, generate a YesQuorum value using GenQuorum(r) with the key "yes_quorum".
    *   Ensure YesQuorum random draw occurs after quorum and before threshold draws.
    *   Pass generated YesQuorum.String() to NewParams().

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.