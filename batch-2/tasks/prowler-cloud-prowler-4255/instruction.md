Implement enhancements to the cloud security scanning tool to include inline IAM policies in privilege escalation and unrestricted access checks. Update existing checks to handle "allow everything except" constructs and centralize privilege escalation logic in a shared module.

*   Create a new module at `prowler/providers/aws/services/iam/lib/privilege_escalation.py`:
    *   Define `privilege_escalation_policies_combination` as a dictionary mapping scenario names to sets of IAM action strings.
    *   Implement `find_privilege_escalation_combinations(allowed_actions: set, denied_actions: set, denied_not_actions: set) -> set` to return privilege-escalating actions, considering wildcard expansions and exclusions.
    *   Implement `check_privilege_escalation(policy: dict) -> str` to parse IAM policy documents, detect escalation paths, and return a descriptive string.

*   Add `check_full_service_access(service: str, policy: dict) -> bool` in `prowler/providers/aws/services/iam/lib/policy.py`:
    *   Return `True` if a policy grants full access to a service using "service:*" or NotAction constructs.
    *   Return `False` if access is restricted by resource or partial wildcards.

*   Develop a Prowler check class `iam_inline_policy_allows_privilege_escalation` in `prowler/providers/aws/services/iam/iam_inline_policy_allows_privilege_escalation/iam_inline_policy_allows_privilege_escalation.py`:
    *   Evaluate inline IAM policies for privilege escalation.
    *   Produce findings with `resource_id`, `resource_arn`, `resource_tags`, and `region`.
    *   Set `status` and `status_extended` based on escalation detection.

*   Develop a Prowler check class `iam_inline_policy_no_full_access_to_cloudtrail` in `prowler/providers/aws/services/iam/iam_inline_policy_no_full_access_to_cloudtrail/iam_inline_policy_no_full_access_to_cloudtrail.py`:
    *   Evaluate inline policies for full CloudTrail access.
    *   Set `status` and `status_extended` based on access detection.

*   Develop a Prowler check class `iam_inline_policy_no_full_access_to_kms` in `prowler/providers/aws/services/iam/iam_inline_policy_no_full_access_to_kms/iam_inline_policy_no_full_access_to_kms.py`:
    *   Evaluate inline policies for full KMS access.
    *   Set `status` and `status_extended` based on access detection.

*   Update existing checks:
    *   Modify `iam_policy_no_full_access_to_cloudtrail` to use `check_full_service_access` for CloudTrail access detection.
    *   Modify `iam_policy_no_full_access_to_kms` similarly for KMS access.
    *   Update `iam_policy_allows_privilege_escalation` to import `privilege_escalation_policies_combination` from the new module.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.