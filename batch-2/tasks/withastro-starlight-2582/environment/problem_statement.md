## Description

The Starlight Tailwind integration package has a failing test related to the order of CSS sections emitted when Tailwind's default browser reset is disabled in favour of Starlight's own base styles.

A newer version of the CSS framework changed the sequence in which it emits CSS during base-style generation. Previously, the internal CSS variable declarations that the framework uses for its utility classes were emitted at the end. In the newer version they are emitted at the beginning — before the standard browser reset rules (default borders, font families) and before Starlight's own theme colour variables.

The integration package's development dependencies still pin to the older version, so the CSS it generates in tests does not match what users running the newer framework version would actually receive.

## Expected Behavior

- The integration package's development dependencies should be aligned with the newer version of the CSS framework that generates the CSS in the expected order.
- When the integration processes base styles, the output should produce the CSS framework's internal CSS variable declarations as the first block, followed by the standard reset styles and Starlight theme variables.

## Why This Matters

Users upgrading to newer versions of the CSS framework in their Starlight projects need the Starlight Tailwind integration to be validated against the same version, so the integration's behaviour is correctly tested and documented.
