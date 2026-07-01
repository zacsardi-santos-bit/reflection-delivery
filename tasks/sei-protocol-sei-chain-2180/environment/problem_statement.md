## Description

The staking precompile currently supports delegating, redelegating, and undelegating tokens, but it provides no way for EVM users or smart contracts to **create a new validator** or **edit an existing validator's parameters**. This is a significant gap: operators who want to set up or manage validators entirely through the EVM layer are unable to do so.

## Expected Behavior

- A new operation should allow a caller to register a new validator by supplying a public key (in hex), a display name, commission settings (rate, max rate, and max daily change rate), a minimum self-delegation amount, and an attached funding amount. On success, the validator should be visible in the staking module with the provided display name.
- A new operation should allow an existing validator's operator to update the validator's display name, commission rate, and minimum self-delegation. Passing an empty string for the commission rate should leave it unchanged. Passing zero for the minimum self-delegation should leave it unchanged. The operator address and consensus public key must never change as a result of an edit.
- Both operations must require the calling address to be linked to a native chain address; unlinked addresses must receive a clear error.
- Both operations must validate all inputs and return descriptive errors: invalid public key format, missing funding amount, unparseable commission values, zero self-delegation, and non-existent validators should all produce clear, specific error messages.

## Why This Matters

Validator lifecycle management (creation and editing) through the EVM is essential for operators who rely on EVM tooling to manage their infrastructure. Without these operations, the staking precompile is incomplete for validator operators, forcing them to use separate native transactions rather than a unified EVM-based workflow.
