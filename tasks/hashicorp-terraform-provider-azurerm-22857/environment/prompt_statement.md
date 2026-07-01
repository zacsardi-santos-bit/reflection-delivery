I'm working with the Terraform provider for Azure and I need to add support for associating automanage configuration profiles with Azure Stack HCI clusters. Right now, the Stack HCI cluster resource doesn't have any attribute for specifying an automanage configuration assignment, so I can't manage that relationship through Terraform at all.

I also need the provider to correctly handle the resource ID format used for automanage configuration assignments when they're scoped to an HCI cluster — this format is different from the one used for regular VM automanage assignments, so I need new parsing and validation logic specifically for this resource type.

The parsing logic should be able to extract the subscription, resource group, cluster name, and configuration profile assignment name from the ID. It should correctly reject malformed IDs including empty strings, IDs with missing segments, and IDs where the path keywords are in the wrong case. The validation utility should enforce the same constraints so that users get clear feedback when they supply an incorrect ID.

On the resource side, I'd like users to be able to set the automanage configuration ID when creating or updating an HCI cluster, and also to remove that assignment later without any errors.
