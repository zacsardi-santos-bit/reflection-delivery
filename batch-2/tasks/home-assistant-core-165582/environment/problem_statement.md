## Description

The energy dashboard allows users to configure how costs are tracked for grid, gas, and water energy sources. Costs can be calculated via a price entity, a fixed numeric rate, or a pre-computed cost statistic from the data source itself.

Some integrations (like utility company integrations) supply their own "external" statistics — data that doesn't come from standard Home Assistant entities but from provider-specific sources with their own IDs. These external statistics often already include cost data bundled in, so combining them with an entity-based or numeric price creates a conflicting and incorrect configuration.

## Problem

Currently, the energy configuration schemas allow users to save energy source configurations that pair an external statistic with an entity price or a numeric price — even though this doesn't work correctly and leads to inaccurate cost tracking. There is no validation at configuration time to prevent this invalid combination.

## Expected Behavior

- When an external statistic is used as the energy source, configuring an entity price or a numeric price alongside it (with no associated cost statistic) should be rejected with a clear error indicating that price configuration is not supported for external statistics.
- If the user has already configured a pre-computed cost statistic alongside the external statistic, then price fields should be allowed (since the cost statistic takes precedence and the price fields are effectively ignored in that case).
- This validation should apply to all relevant energy source types: grid import, grid export, gas, and water.

## Why This Matters

Without this validation, users who configure an external statistic (e.g., from a utility company integration) with a separate price entity will end up with incorrect or confusing cost data in their energy dashboard. Catching this at configuration save time gives users a clear error and guides them toward a valid setup.
