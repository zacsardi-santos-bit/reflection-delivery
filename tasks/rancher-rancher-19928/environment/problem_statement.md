## Cloud Provider Password Fields Cleared on Cluster Update

When users update a Kubernetes cluster that has a cloud provider configured (Azure, vSphere, or OpenStack), password and secret fields are often intentionally omitted from the update request because they are sensitive credentials that should not be round-tripped through the API. However, the current system does not restore these omitted secrets from the saved cluster configuration before persisting the update. As a result, password fields are silently cleared, causing the cloud provider integration to break after any routine cluster update that does not explicitly re-supply every secret.

This is especially problematic for vSphere clusters with multiple virtual center entries: if a user updates configuration for one virtual center without including its password, that password is lost. Similarly, if a user adds or removes a virtual center, the passwords for the other virtual centers that were not touched may be wiped.

Users expect that password fields they previously set will be preserved across updates unless they explicitly supply new values. The system should detect when a password field has been omitted and automatically restore it from the stored configuration, while still allowing intentional password changes when a new value is provided.
