I'm working on the project-scoped secrets controller in Rancher.

*   A new exported constant named ProjectScopedSecretClusterLabel must be defined in pkg/controllers/managementuser/secret/project_scoped_secrets.go with the string value "management.cattle.io/project-scoped-secret-cluster".

*   The method previously named migrateExistingProjectScopedSecrets on namespaceHandler must be renamed to migrateExistingNormanProjectScopedSecrets; it must retain the same signature (project *v3.Project) error and, in addition to existing behavior, must also set the ProjectScopedSecretClusterLabel label to the cluster name when updating migrated secrets.

*   A new method ensureProjectScopeSecretClusterLabel(project *v3.Project) error must be added to namespaceHandler in pkg/controllers/managementuser/secret/project_scoped_secrets.go. It must list all secrets in the project's backing namespace that have the ProjectScopedSecretLabel label, return an error if listing fails, skip secrets that already carry ProjectScopedSecretClusterLabel, and for secrets missing that label set it to project.Spec.ClusterName and call Update. It must accumulate and return any update errors.

*   When ensureProjectScopeSecretClusterLabel processes multiple secrets and some update calls fail, it must accumulate all errors and return a non-nil error; it must not stop processing on the first failure.

*   Secrets retrieved by getProjectScopedSecretsFromNamespace must carry both ProjectScopedSecretLabel and ProjectScopedSecretClusterLabel labels.


*   Interface details: Type: Constant
Name: ProjectScopedSecretClusterLabel
Location: pkg/controllers/managementuser/secret/project_scoped_secrets.go
Value: "management.cattle.io/project-scoped-secret-cluster"
Description: Exported string constant used as the label key for identifying the cluster a project-scoped secret belongs to.

Type: Method
Name: migrateExistingNormanProjectScopedSecrets
Location: pkg/controllers/managementuser/secret/project_scoped_secrets.go
Signature: (n *namespaceHandler) migrateExistingNormanProjectScopedSecrets(project *v3.Project) error
Description: Renamed from migrateExistingProjectScopedSecrets. Migrates old Norman-style project-scoped secrets by removing legacy finalizers/annotations and adding both ProjectScopedSecretLabel and ProjectScopedSecretClusterLabel labels. Must set ProjectScopedSecretClusterLabel to the cluster name on each migrated secret.

Type: Method
Name: ensureProjectScopeSecretClusterLabel
Location: pkg/controllers/managementuser/secret/project_scoped_secrets.go
Signature: (n *namespaceHandler) ensureProjectScopeSecretClusterLabel(project *v3.Project) error
Description: Lists all secrets in the project's backing namespace that have the ProjectScopedSecretLabel label. For each secret missing ProjectScopedSecretClusterLabel, sets that label to project.Spec.ClusterName and calls Update on the management secret client. Secrets that already have the cluster label are skipped. Returns an error if listing fails. Accumulates and returns all update errors without stopping on the first failure.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.