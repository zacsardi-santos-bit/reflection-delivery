I'm working on a Microsoft 365 security scanner and need to add two new checks related to Teams security reporting. Right now the tool has no way to tell whether the organization's Teams environment is properly configured for users to report suspicious or malicious messages, or whether the backend submission policy correctly routes those reports to the right mailboxes.

The first check should look at the global Teams messaging policy and verify that end users are allowed to report security concerns from their chat interface. If the policy is present and the setting is enabled, the check should pass; if disabled, it should fail; and if no policy data is available at all, it should produce no finding.

The second check should look at the Defender report submission policy and verify that it's fully configured to send reported content to custom organizational addresses — covering junk, non-junk, and phishing categories — with non-empty destination address lists, and that chat message reporting to Microsoft directly is disabled while reporting to a custom address is enabled. Any misconfiguration should result in a failure finding. If the policy doesn't exist, no finding should be produced.

Both the Teams service and the Defender service need to be updated to fetch and store these new policy objects so the checks can access them.
