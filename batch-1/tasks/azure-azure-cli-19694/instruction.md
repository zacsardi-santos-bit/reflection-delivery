Implement the ability to attach and detach Azure Container Registries (ACR) during an AKS cluster update. Develop utility functions and methods to determine the authentication type of a cluster, retrieve the correct client ID, and manage ACR access during updates.

*   Implement `check_is_msi_cluster(mc)` function:
    *   Accept a `ManagedCluster` object or `None`.
    *   Return `False` if `mc` is `None`.
    *   Return `True` if `mc.identity.type` is `"SystemAssigned"` or `"UserAssigned"`.
    *   Return `False` for any other identity type.

*   Update `AKSContext` class:
    *   Implement `get_detach_acr(self)` method:
        *   Return the raw value of the "detach_acr" parameter.
        *   Return `None` if the parameter is not set.
        *   Ensure it functions in `DecoratorMode.UPDATE`.
    *   Ensure `get_attach_acr` method operates without exceptions in `DecoratorMode.UPDATE` when "attach_acr", "enable_managed_identity", and "no_wait" parameters are provided.
    *   Implement `get_client_id_from_identity_or_sp_profile(self)` method:
        *   Raise `UnknownError` if no `ManagedCluster` is attached or no client ID can be resolved.
        *   For MSI clusters with `SystemAssigned` identity, raise `UnknownError` if `kubeletidentity` is not found in `identity_profile`.
        *   Return `identity_profile['kubeletidentity'].client_id` for `UserAssigned` MSI clusters with a configured kubelet identity.
        *   Return `service_principal_profile.client_id` for non-MSI clusters.

*   Update `AKSUpdateDecorator` class:
    *   Implement `process_attach_detach_acr(self, mc)` method:
        *   Raise `CLIInternalError` if `mc` does not match the `ManagedCluster` attached to the context.
        *   When "attach_acr" is set, call `_ensure_aks_acr` with `cmd`, `client_id` (from `get_client_id_from_identity_or_sp_profile`), `acr_name_or_id=<attach_acr value>`, and `subscription_id` (from context intermediate "subscription_id").
        *   When "detach_acr" is set, call `_ensure_aks_acr` with the same parameters plus `detach=True`.
        *   Do not call `_ensure_aks_acr` if both "attach_acr" and "detach_acr" are `None`.
        *   Call `_ensure_aks_acr` for attach first and then for detach if both are set.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.