## Description

The Microsoft 365 security scanner is missing two important checks related to Teams security reporting. Organizations need a way to automatically audit whether their Teams environment is configured to let users report suspicious or malicious messages, and whether the Defender submission policy is set up to properly route those reports to dedicated security mailboxes.

Currently, there is no check that verifies:
1. Whether the global Teams messaging policy allows end users to flag security concerns directly from their chat experience.
2. Whether the Defender report submission policy is configured to forward junk, phishing, and chat security reports to custom addresses (rather than to Microsoft by default), with chat message reporting to Microsoft disabled.

## Expected Behavior

- A new check should inspect the global Teams messaging policy and report PASS when end-user security reporting is enabled, or FAIL when it is not. If no policy data is available, no finding should be produced.
- A separate new check should inspect the Defender report submission policy and report PASS only when all three categories of customized-address reporting (junk, not-junk, and phishing) are enabled with non-empty address lists, chat-to-Microsoft reporting is disabled, and chat-to-custom-address reporting is enabled. Any deviation results in a FAIL. If no policy exists, no finding is produced.

## Why This Matters

Without these automated checks, administrators have no visibility into misconfigurations that would prevent user-reported threats from reaching the security team, leaving the organization blind to user-flagged attacks in Teams.
