I'm working on improving the security check suite in our cloud compliance tool and need two things done.

*   The Azure check for verifying MFA is required for management API access must be renamed to `entra_conditional_access_policy_require_mfa_for_management_api` and located at `prowler/providers/azure/services/entra/entra_conditional_access_policy_require_mfa_for_management_api/entra_conditional_access_policy_require_mfa_for_management_api.py`.

*   For the renamed Azure check, all findings (both PASS and FAIL) must report `resource_name` as the string `"Conditional Access Policy"` and `resource_id` as the string `"Conditional Access Policy"`, regardless of the specific tenant or policy involved.

*   The M365 Entra service module (`prowler/providers/m365/services/entra/entra_service.py`) must export three new data model classes: `CredentialRestriction`, `AppManagementRestrictions`, and `DefaultAppManagementPolicy`.

*   `CredentialRestriction` must accept fields: `restriction_type` (str), `state` (str), and optional `max_lifetime` (str, defaults to None).

*   `AppManagementRestrictions` must accept fields `password_credentials` (list of `CredentialRestriction`, defaults to empty list) and `key_credentials` (list of `CredentialRestriction`, defaults to empty list).

*   `DefaultAppManagementPolicy` must accept fields: `id` (str), `name` (str), `description` (str or None), `is_enabled` (bool), and optional `application_restrictions` (an `AppManagementRestrictions` instance).

*   The `Entra` service class must have an async method `_get_default_app_management_policy` that fetches and returns a `DefaultAppManagementPolicy` object, and a `default_app_management_policy` attribute populated by calling that method.

*   A new M365 check class `entra_default_app_management_policy_enabled` must be created at `prowler/providers/m365/services/entra/entra_default_app_management_policy_enabled/entra_default_app_management_policy_enabled.py`. It must read `entra_client.default_app_management_policy` and `entra_client.tenant_domain`.

*   When `entra_client.default_app_management_policy` is `None`, the check must return an empty list of findings.

*   When the policy's `is_enabled` field is `False`, the check must return one FAIL finding with `"not enabled"` appearing in `status_extended`.

*   When the policy is enabled, the check must verify that each of the following credential restrictions is present and has `state == "enabled"`: `passwordAddition` (password credentials), `passwordLifetime` (password credentials), `customPasswordAddition` (password credentials), and `asymmetricKeyLifetime` (key credentials). A restriction with `state != "enabled"` is treated as missing.

*   When one or more required restrictions are missing or disabled, the check must return one FAIL finding. The `status_extended` must include a specific phrase for each missing or disabled restriction: `"Block password addition"` for `passwordAddition`, `"Restrict max password lifetime"` for `passwordLifetime`, `"Block custom passwords"` for `customPasswordAddition`, and `"Restrict max certificate lifetime"` for `asymmetricKeyLifetime`.

*   When all required restrictions are present and enabled, the check must return one PASS finding with `"all required credential restrictions"` in `status_extended`.

*   The `resource_name` for all findings from the M365 check must be the string `"Default App Management Policy"`.

*   The `resource_id` for findings from the M365 check must be the policy's `id` field, or fall back to `entra_client.tenant_domain` when the policy `id` is an empty string.


*   Interface details: Type: Class
Name: entra_conditional_access_policy_require_mfa_for_management_api
Location: prowler/providers/azure/services/entra/entra_conditional_access_policy_require_mfa_for_management_api/entra_conditional_access_policy_require_mfa_for_management_api.py
Description: Renamed Azure check class that verifies whether a conditional access policy enforces MFA for management API access. The check reads `entra_client.conditional_access_policy` and produces findings with `resource_name="Conditional Access Policy"` and `resource_id="Conditional Access Policy"` in all cases.
Signature: execute() -> list[CheckResult]

Type: Class
Name: entra_default_app_management_policy_enabled
Location: prowler/providers/m365/services/entra/entra_default_app_management_policy_enabled/entra_default_app_management_policy_enabled.py
Description: New M365 check class that verifies whether the default application management policy is enabled and has all required credential restrictions active. Reads `entra_client.default_app_management_policy` and `entra_client.tenant_domain`. Returns no findings when the policy is None. Returns FAIL with "not enabled" in `status_extended` when the policy's `is_enabled` is False. Returns FAIL listing any missing or disabled restriction phrases when restrictions are absent or disabled. Returns PASS with "all required credential restrictions" in `status_extended` when all restrictions pass. Uses `resource_name="Default App Management Policy"`, `resource_id=policy.id` or `entra_client.tenant_domain` when id is empty.
Signature: execute() -> list[CheckResult]

Type: Class
Name: CredentialRestriction
Location: prowler/providers/m365/services/entra/entra_service.py
Description: Data model representing a single credential restriction rule. Instances are created with keyword arguments `restriction_type` (str), `state` (str), and optional `max_lifetime` (str, defaults to None).
Signature: CredentialRestriction(restriction_type: str, state: str, max_lifetime: str | None = None)

Type: Class
Name: AppManagementRestrictions
Location: prowler/providers/m365/services/entra/entra_service.py
Description: Data model representing the credential restriction collections for an application management policy. Accepts `password_credentials` (list of CredentialRestriction, defaults to empty list) and `key_credentials` (list of CredentialRestriction, defaults to empty list).
Signature: AppManagementRestrictions(password_credentials: list[CredentialRestriction] = [], key_credentials: list[CredentialRestriction] = [])

Type: Class
Name: DefaultAppManagementPolicy
Location: prowler/providers/m365/services/entra/entra_service.py
Description: Data model representing the default application management policy for a tenant. Fields: `id` (str), `name` (str), `description` (str or None), `is_enabled` (bool), and optional `application_restrictions` (AppManagementRestrictions).
Signature: DefaultAppManagementPolicy(id: str, name: str, description: str | None, is_enabled: bool, application_restrictions: AppManagementRestrictions | None = None)

Type: Method
Name: _get_default_app_management_policy
Location: prowler/providers/m365/services/entra/entra_service.py (on the Entra class)
Description: Async method on the Entra service class that fetches and returns a DefaultAppManagementPolicy object. The result is stored as `entra_client.default_app_management_policy` on the Entra instance.
Signature: async _get_default_app_management_policy(self) -> DefaultAppManagementPolicy


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.