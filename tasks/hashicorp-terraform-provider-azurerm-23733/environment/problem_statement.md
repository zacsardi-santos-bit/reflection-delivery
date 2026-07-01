## Description

Azure NetApp Files currently does not support customer-managed key (CMK) encryption at the account or volume level through this provider. All data is silently encrypted using Microsoft-managed keys, with no way for users to bring their own Key Vault key or specify the identity used to access it. Additionally, NetApp accounts cannot be configured with a managed identity (system-assigned or user-assigned), which is a prerequisite for CMK encryption.

## Expected Behavior

- It should be possible to assign a managed identity (system-assigned or user-assigned) to a NetApp account, and update the identity type after initial creation.
- Users should be able to retrieve managed identity information (including type and principal ID) from both the account resource and its data source.
- A dedicated resource should exist for managing the CMK encryption settings of a NetApp account, allowing users to associate the account with a Key Vault key using either a system-assigned or user-assigned managed identity.
- The encryption configuration should be updatable (e.g., rotating to a new key) without destroying the account.
- A corresponding data source should allow reading the current encryption settings of a NetApp account.
- NetApp volumes should support specifying the encryption key source (customer-managed vs. Microsoft-managed) and a private endpoint to securely connect to the Key Vault.
- The snapshot directory visibility on a volume should be configurable to either visible or hidden.
- The NetApp volume data source should expose the encryption key source so operators can verify the encryption configuration.
- A validator for the NetApp account resource ID format should be available to catch malformed identifiers early.

## Why This Matters

Organizations with strict data security requirements often need to control their own encryption keys (customer-managed keys) rather than relying on Microsoft-managed encryption. Without support for managed identities and CMK configuration on NetApp accounts and volumes, users cannot meet these compliance requirements through Terraform. This feature brings the provider up to parity with what the Azure NetApp Files API already supports.
