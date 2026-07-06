## Description

When Cypress is run on a 32-bit Windows machine, users receive no indication that this platform is being deprecated and will lose support in a future release. Without any warning, users may be caught off guard when support is eventually dropped.

## Expected Behavior

- During project initialization, Cypress should detect whether it is running on a 32-bit Windows system
- If a 32-bit Windows environment is detected, Cypress should surface a warning to the user through the existing warning notification mechanism
- The warning message should clearly state that the user is running a 32-bit build
- The warning should only be triggered on 32-bit Windows; users on other operating systems or 64-bit Windows should not see it

## Why This Matters

Users running Cypress on 32-bit Windows deserve advance notice about the upcoming deprecation so they can plan a migration to a 64-bit environment before support ends. Without this warning, users have no way of knowing their configuration will be unsupported until it is too late.

Relates to: https://github.com/cypress-io/cypress/issues/18094
