Implement a new lint rule named 'noSecrets' to detect hardcoded secrets in JavaScript and TypeScript files. This rule should identify and flag sensitive credentials like API keys, tokens, and other secrets, providing guidance on how to address them.

*   Create the 'noSecrets' lint rule in the file `crates/biome_js_analyze/src/lint/nursery/no_secrets.rs`.
    *   Register this rule in `crates/biome_js_analyze/src/lint/nursery.rs` under the nursery category.
*   Ensure the rule scans string literals and reports diagnostics with the message 'Potential secret found.' with '!' severity.
*   Include an info note in each diagnostic: 'Type of secret detected: [LABEL]' where LABEL identifies the secret type.
*   Add a second info note with remediation steps:
    *   '1. Remove the secret from your code. If you've already committed it, consider removing the commit entirely from your git tree.'
    *   '2. If needed, use environment variables or a secure secret management system to store sensitive data.'
    *   '3. If this is a false positive, consider adding an inline disable comment.'
*   Detect specific patterns and label them accordingly:
    *   AWS API keys: 'AWS API Key'
    *   Slack tokens: 'Slack Token'
    *   RSA private key material: 'RSA Private Key'
    *   Facebook OAuth tokens: 'Facebook OAuth'
    *   Twitter OAuth keys: 'Twitter OAuth'
    *   GitHub personal access tokens: 'GitHub'
    *   Slack webhook URLs: 'Slack Webhook'
    *   Twilio API keys: 'Twilio API Key'
    *   Database connection strings with hardcoded passwords: 'Password in URL'
*   Implement entropy-based detection for high-entropy strings, labeling them 'The string has a high entropy value'. Ensure strings like 'abcdefghijklmnopqrstuvwxyz' are flagged.
*   Exclude from flagging:
    *   Numeric literals
    *   Short strings (fewer than 12 characters)
    *   Low entropy prose
    *   Template literals using runtime variable references

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.