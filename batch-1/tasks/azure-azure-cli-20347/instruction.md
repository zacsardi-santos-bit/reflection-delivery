Implement the `update_identity` method in the `AKSUpdateDecorator` class to support updating the managed identity configuration of an existing AKS cluster. Ensure the method handles identity type changes, user confirmations, and error conditions as specified.

*   Implement `update_identity` as an instance method in `AKSUpdateDecorator` located at `src/azure-cli/azure/cli/command_modules/acs/decorator.py`.
*   Ensure `update_identity` accepts a `ManagedCluster` object `mc` as a parameter and returns a `ManagedCluster` object.
*   Raise `CLIInternalError` if `mc` is not a valid `ManagedCluster` object (e.g., `None`).
*   Return the `ManagedCluster` object unchanged if:
    *   `enable_managed_identity` is `False` or not set.
    *   `assign_identity` is `None`.
*   Raise `RequiredArgumentMissingError` if `assign_identity` is provided but `enable_managed_identity` is `False`.
*   Handle identity type changes:
    *   If a change is required and `prompt_y_n` returns `False` and `yes` is not `True`, raise `DecoratorEarlyExitException`.
    *   If `enable_managed_identity` is `True` and `assign_identity` is provided, and either `yes=True` or the user confirms the prompt:
        *   Set `mc.identity` to `ManagedClusterIdentity(type='UserAssigned', user_assigned_identities={assign_identity: ManagedServiceIdentityUserAssignedIdentitiesValue()})`.
    *   If `enable_managed_identity` is `True` and `assign_identity` is `None`, and either `yes=True` or the user confirms the prompt:
        *   Set `mc.identity` to `ManagedClusterIdentity(type='SystemAssigned')` with no `user_assigned_identities`.
*   Ensure parameters (`enable_managed_identity`, `assign_identity`, `yes`) are read from the decorator's context, not directly from `mc`.
*   Use `context.attach_mc(mc)` to link the cluster before calling `update_identity`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.