Implement two new checks in the Microsoft 365 security scanner to audit Teams security reporting configurations. Ensure the scanner verifies if the Teams environment allows users to report suspicious messages and if the Defender submission policy routes reports to the correct mailboxes.

*   Update the `prowler/providers/m365/services/defender/defender_service.py` file:
    *   Define the `ReportSubmissionPolicy` class as a Pydantic BaseModel with the following fields:
        *   `report_junk_to_customized_address`: bool
        *   `report_not_junk_to_customized_address`: bool
        *   `report_phish_to_customized_address`: bool
        *   `report_junk_addresses`: list[str]
        *   `report_not_junk_addresses`: list[str]
        *   `report_phish_addresses`: list[str]
        *   `report_chat_message_enabled`: bool
        *   `report_chat_message_to_customized_address_enabled`: bool
    *   Ensure the `Defender` class initializes `report_submission_policy` to None and populates it by calling `_get_report_submission_policy()`.
    *   Implement `_get_report_submission_policy(self) -> ReportSubmissionPolicy | None` to retrieve the policy via PowerShell.

*   Update the `defender_chat_report_policy_configured` check in `prowler/providers/m365/services/defender/defender_chat_report_policy_configured/defender_chat_report_policy_configured.py`:
    *   Implement `execute(self) -> List[CheckReportM365]` to:
        *   Return an empty list if `report_submission_policy` is None.
        *   Return a PASS result if all conditions for customized-address reporting are met and chat-to-Microsoft reporting is disabled.
        *   Return a FAIL result if any condition is not met.
        *   Each result must include `resource_name`, `resource_id`, `location`, and `resource` details.

*   Update the `prowler/providers/m365/services/teams/teams_service.py` file:
    *   Define the `GlobalMessagingPolicy` class as a Pydantic BaseModel with the field:
        *   `allow_security_end_user_reporting`: bool = False
    *   Ensure the `Teams` class initializes `global_messaging_policy` to None and populates it by calling `_get_global_messaging_policy()`.
    *   Implement `_get_global_messaging_policy(self) -> GlobalMessagingPolicy | None` to retrieve the policy via PowerShell.

*   Update the `teams_security_reporting_enabled` check in `prowler/providers/m365/services/teams/teams_security_reporting_enabled/teams_security_reporting_enabled.py`:
    *   Implement `execute(self) -> List[CheckReportM365]` to:
        *   Return an empty list if `global_messaging_policy` is None.
        *   Return a PASS result if `allow_security_end_user_reporting` is True, including `resource_name` and `resource_id`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.