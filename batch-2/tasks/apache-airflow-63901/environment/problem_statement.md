## Description

When a CI job fails in GitHub Actions, contributors need to reproduce the failure locally. Currently, there's no automated way for the CI system to show the exact commands needed — which commit to check out, which image to build, and which CLI invocation to run with the exact same flags.

This means contributors must manually piece together: the right git ref to fetch, the specific commit SHA to check out, which Python version and platform to use for the image build, and every option that was active during the CI run. This is tedious, error-prone, and wastes time.

## Expected Behavior

- At the end of every Breeze CLI command run in GitHub Actions, the CI log should automatically display a "how to reproduce locally" block
- The block should include the exact git commands to fetch and check out the same code that ran in CI
- For pull-request builds, the fetch command should include a human-friendly note about the PR number
- The block should include the full Breeze CLI invocation with all non-default options that were active — whether they were set via the command line or environment variables
- The reproduction block should only appear when genuinely running in GitHub Actions (when both the CI indicator and the GitHub Actions indicator environment variables confirm a CI context); it should not appear during local development

## Why This Matters

Contributors debugging CI failures no longer have to guess which flags were active or which commit was used. They get a ready-to-run sequence of commands they can copy directly from the CI log.
