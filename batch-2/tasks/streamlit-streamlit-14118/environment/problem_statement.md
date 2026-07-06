## Description

When Streamlit starts, it always prints a welcome message and URL to the console. There is currently no built-in way to suppress this output through configuration alone. This causes issues for users running Streamlit in automated environments, CI/CD pipelines, or as embedded services where the startup banner clutters logs or is simply unwanted noise.

## Expected Behavior

- A new option should be available under the logger configuration section that allows users to hide the startup welcome message entirely.
- When this option is enabled, starting a Streamlit app should produce no welcome/URL output to the console.
- When this option is not set or disabled, the existing behavior (displaying the welcome message and URL) should remain unchanged.

## Why This Matters

Many production and automated deployments benefit from quiet startup behavior. Having a supported configuration option for this allows users to suppress the startup banner without resorting to output redirection or monkey-patching, keeping their logs clean and focused on application-level messages.
