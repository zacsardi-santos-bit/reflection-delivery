Implement three new machine-scoped commands in the Microsoft Defender for Endpoint integration to retrieve installed software, missing security updates, and vulnerabilities for a specific machine. Ensure each command accepts a machine ID as input and returns structured outputs with relevant details.

*   Implement `get_machine_software_command(client: MsClient, args: dict) -> CommandResults`
    *   Read `machine_id` from `args`.
    *   Call `client.get_software_by_machine_id(machine_id)`.
    *   Return a `CommandResults` object with:
        *   `outputs`: List of dicts from the response 'value' array, including fields: ID, Name, Vendor, PublicExploit, ActiveAlerts, ExposedMachines, InstalledMachines, ImpactScore, IsNormalized (omit None/empty).
        *   `readable_output`: Markdown table with heading '### Microsoft Defender ATP software on machine: {machine_id}' and columns: ID, Name, Vendor, PublicExploit, ExposedMachines, InstalledMachines, ImpactScore, IsNormalized (omit Null/empty).

*   Implement `get_machine_missing_kbs_command(client: MsClient, args: dict) -> CommandResults`
    *   Read `machine_id` from `args`.
    *   Call `client.get_missing_kbs_by_machine_id(machine_id)`.
    *   Return a `CommandResults` object with:
        *   `outputs`: List of dicts from the response 'value' array, including fields: ID, Name, OSBuild, URL, CVEAddressed (omit None/empty).
        *   `readable_output`: Markdown table with heading '### Missing Security Updates (KBs) for machine: {machine_id}' and columns: ID, Name, OSBuild, URL, CVEAddressed (omit Null/empty).

*   Implement `get_machine_vulnerabilities_command(client: MsClient, args: dict) -> CommandResults`
    *   Read `machine_id` from `args`.
    *   Call `client.get_vulnerabilities_by_machine_id(machine_id)`.
    *   Return a `CommandResults` object with:
        *   `outputs`: List of dicts from the response 'value' array, including fields: ID, Name, CVESupportability, CVSSV3, CVSSVector, Description, EPSS, ExploitInKit, ExploitTypes, ExploitVerified, ExposedMachines, FirstDetected, PublicExploit, PublishedOn, Severity, UpdatedOn (omit None/empty).
        *   `readable_output`: Markdown table with heading '### Microsoft Defender ATP Vulnerabilities for machine: {machine_id}' and columns: ID, Name, CVESupportability, CVSSV3, CVSSVector, Description, EPSS, ExploitInKit, ExploitTypes, ExploitVerified, ExposedMachines, FirstDetected, PublicExploit, PublishedOn, Severity, UpdatedOn (omit Null/empty).

*   Update `MsClient` class with:
    *   Method `get_software_by_machine_id(self, machine_id: str) -> dict`
        *   Retrieve installed software inventory for the given machine via the API.
    *   Method `get_missing_kbs_by_machine_id(self, machine_id: str) -> dict`
        *   Retrieve missing security updates for the given machine via the API.
    *   Method `get_vulnerabilities_by_machine_id(self, machine_id: str) -> dict`
        *   Retrieve vulnerabilities for the given machine via the API.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.