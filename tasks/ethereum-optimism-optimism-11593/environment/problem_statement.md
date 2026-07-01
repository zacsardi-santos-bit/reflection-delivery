## Description

The OP Chain deployment tooling needs structured, validated contracts for managing deployment inputs and outputs. Currently there is no standardized way to:
1. Declare and validate all required configuration (operator roles, fee scalars, chain ID) before deploying a new OP Chain
2. Record the resulting deployed contract addresses after deployment in a typed, safety-checked manner

This leads to silent failures — missing inputs go undetected, and zero-address outputs can be returned without any indication that something went wrong.

## Expected Behavior

- A deployment input contract should accept all required configuration as a typed structure, expose individual getters for each parameter, and guard against access before inputs are configured (returning a clear error message).
- A deployment output contract should record all deployed contract addresses indexed by contract type, expose typed getters for each address, and reject access to addresses that are zero or point to accounts with no deployed code (with clear error messages indicating the specific failure).
- Both contracts should be co-located in a single deployment script file and re-exported together alongside the main deployment script contract.

## Why This Matters

Without these guardrails, deployment scripts can silently proceed with missing or invalid inputs, or callers may receive zero addresses without realizing deployment failed. These contracts make misconfiguration and incomplete deployments immediately visible with actionable error messages.
