## Description

The AI gateway budget policy management endpoints currently lack proper access controls. Any authenticated user can create, update, or delete budget policies, which should be restricted to administrators only. Budget policies control spending limits and financial guardrails for the gateway, so allowing non-admin users to modify them is a security concern.

## Expected Behavior

- Admin users can create, update, and delete budget policies
- Non-admin users attempting to create, update, or delete a budget policy should receive a **403 Forbidden** response
- Non-admin users should be able to **read** budget policies — both listing all policies and retrieving a specific policy by ID should be permitted for any authenticated user

## Why This Matters

Budget policies are financial controls that govern how much spending the gateway allows. Only administrators should have the ability to create or change these policies. Regular authenticated users should still be able to view existing policies (for transparency), but write operations must be admin-only to prevent unauthorized changes to financial limits.
