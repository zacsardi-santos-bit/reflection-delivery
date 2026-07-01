## Description

The governance module currently supports a participation quorum — a minimum fraction of total staked tokens that must vote before a proposal is considered valid. However, it lacks a separate requirement for how many of those total staked tokens must specifically vote **yes** for a proposal to pass. This means a proposal can pass with yes votes that are a large fraction of participants but a small fraction of total stake.

We need a new governance parameter: a **yes quorum** (the minimum fraction of total voting power that must vote in favor of a proposal). When configured, if a proposal does not meet this threshold, it should fail without burning the deposit — treating it similarly to a regular quorum failure.

## Expected Behavior

- The governance parameter set should support a configurable yes quorum field.
- Per-message governance parameters should also support this field, and validation should reject negative values with an appropriate error.
- When tallying votes: if the yes quorum is configured and the yes votes as a fraction of total voting power do not meet the yes quorum, the proposal should fail and the deposit should **not** be burned.
- On chain upgrade, the migration should ensure any chains upgrading from an older version have this new field populated with the appropriate default value rather than left empty.
- Simulation-based genesis generation should include a randomly generated yes quorum value.

## Why This Matters

Without a yes quorum, a governance proposal could pass even if a small absolute fraction of total stake supports it, as long as most active participants voted yes. A yes quorum provides an additional safeguard ensuring a minimum level of explicit support from the overall staked supply.
