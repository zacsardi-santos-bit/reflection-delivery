Implement a flexible ownership tracking mechanism for the secret reconciliator in a Kubernetes operator. Introduce two strategies for managing secrets: one using owner references and another using labels to ensure certain secrets persist after the deletion of the main custom resource.

*   Update the `NewSecretReconciliator` function:
    *   Accept a fourth parameter of type `OwnershipStrategy`.
    *   Define `OwnershipStrategyOwnerReference` and `OwnershipStrategyLabel` constants with values "owner-reference" and "label" respectively.

*   Modify the `EnsureSecret` method in `SecretReconciliator`:
    *   When using `OwnershipStrategyOwnerReference`:
        *   Set both an owner reference pointing to the Central CR and the `ManagedByLabel` with `ManagedByValue` on secrets.
    *   When using `OwnershipStrategyLabel`:
        *   Set only the `ManagedByLabel` with `ManagedByValue` on secrets, without an owner reference.
    *   Consider a secret managed if it has either an owner reference or the correct `ManagedByLabel`.
    *   Treat a secret with neither an owner reference nor the correct label as unmanaged.
    *   When using `OwnershipStrategyLabel`, apply the `ManagedByLabel` if a secret has an owner reference but no label.

*   Specific secret handling:
    *   `central-db-password` secret:
        *   Must not have an owner reference to the Central CR.
        *   Must have the `ManagedByLabel` applied.
        *   Ensure it persists after Central CR deletion.
    *   `central-htpasswd`, `scanner-db-password`, and `scanner-v4-db-password` secrets:
        *   Must have both an owner reference and the `ManagedByLabel` applied.

*   Ensure that after a Central CR is deleted:
    *   The `central-db-password` secret and the `central-db` PVC still exist.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.