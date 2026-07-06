I'm working with Azure NetApp Files in Terraform and I need to configure customer-managed key encryption for my NetApp accounts and volumes, but the provider doesn't seem to support this yet.

Specifically, I need to be able to assign a managed identity to a NetApp account (both system-assigned and user-assigned), and then link that account to a Key Vault key so that data encryption uses my own key rather than a Microsoft-managed one. I'd also like to be able to rotate to a new encryption key later without destroying the resource. There should be both a resource to manage this encryption configuration and a data source to read it back.

On the volume side, I need to specify that a volume should use the customer-managed key encryption from the account's Key Vault, along with a private endpoint to connect securely to the vault. I also need the encryption source to be readable through the volume's data source.

Additionally, I'd like to be able to control whether the snapshot directory is visible on a volume — currently only hiding it works, but I need to explicitly enable visibility too.

Finally, the provider should validate NetApp account resource IDs properly — catching malformed identifiers like missing path segments or incorrectly cased paths — so misconfigurations are caught early before any API calls are made.
