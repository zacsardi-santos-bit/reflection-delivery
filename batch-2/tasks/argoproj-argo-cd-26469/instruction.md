I'm working on Argo CD and noticed that when it reports the Kubernetes server version for a cluster, it only shows the major and minor numbers — something like "1.

*   The GetServerVersion method on KubectlCmd must use the Kubernetes discovery client to retrieve the server version and return the full semantic version string in the format 'v<major>.<minor>.<patch>' (e.g., 'v1.34.0'), derived from the server's GitVersion field — NOT the old 'Major.Minor' two-part format.

*   GetServerVersion must strip provider-specific build metadata or vendor suffixes from the GitVersion (e.g., 'v1.30.11+IKS' must become 'v1.30.11'). The returned string must not contain any '+' or vendor tag characters.

*   When the Kubernetes discovery client fails to retrieve server version information (e.g., the server returns an HTTP error), GetServerVersion must return a non-nil error whose message contains the string 'failed to get server version'.

*   The default ServerVersion value stored for a cluster when only empty version info is available must be '0.0.0' rather than '.' (the two-dot placeholder produced by concatenating empty Major and Minor strings).

*   The Version struct in the test fixture must include a GitVersion string field in addition to the existing Major and Minor fields, and its String() method must return the full semantic version derived from GitVersion (prefixed with 'v'), replacing the previous Major.Minor format. The Format() method must be removed.


*   Interface details: Type: Method
Name: GetServerVersion
Location: gitops-engine/pkg/utils/kube/ctl.go
Signature: GetServerVersion(config *rest.Config) (string, error)
Description: Retrieves the Kubernetes server version using the discovery client and returns it as a full semantic version string in the format "v<major>.<minor>.<patch>" (e.g., "v1.34.0"). Must parse the GitVersion field from the server's version response to obtain the full version. Must strip any provider-specific build metadata or vendor suffixes (e.g., "+IKS") from the version string. Must return an error whose message contains "failed to get server version" when the discovery client fails. The method is on the KubectlCmd struct.

Type: Struct field
Name: GitVersion
Location: test/e2e/fixture/versions.go
Description: The Version struct (within the Versions fixture) must include a GitVersion string field in addition to the existing Major and Minor string fields. This field is populated from the JSON output of the cluster version API. The String() method on Version must return "v" followed by the parsed full semantic version derived from GitVersion (e.g., "v1.30.11"). The Format(format string) method must be removed from the Version struct.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.