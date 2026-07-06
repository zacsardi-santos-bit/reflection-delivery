Revise the threat intelligence feed integration to correctly parse and classify reports from Unit 42 ATOMs data, ensuring both main and sub-reports are processed as output indicators. Update the logic to handle relationships with valid indicator types and centralize attack technique parsing.

*   Ensure `STIX_2_TYPES_TO_CORTEX_TYPES` remains a module-level constant in `Packs/ApiModules/Scripts/TAXII2ApiModule/TAXII2ApiModule.py` and is importable by that name.
*   Implement `STIX2XSOARParser.get_mitre_attack_id_and_value_from_name(attack_indicator: dict) -> tuple` to:
    *   Split the 'name' field at ':' and return a tuple (mitre_id, value).
    *   Use 'x_panw_parent_technique_subtechnique' if 'x_mitre_is_subtechnique' is True.
    *   Raise `DemistoException` with 'Failed parsing attack indicator' if no ':' is present.
*   Ensure reports with relationships use `entityAType` and `entityBType` values from `STIX_2_TYPES_TO_CORTEX_TYPES.values()`.
    *   Produce exactly 2 reports with relationships from updated test data.
*   Parse STIX v2.1 envelopes to yield 16 objects, ensuring all relationship entity types are valid.
*   Implement `is_atom42_sub_report(report_obj: dict) -> bool` in `Packs/FeedUnit42v2/Integrations/FeedUnit42v2/FeedUnit42v2.py` to:
    *   Return True if no 'description' and 'object_refs' contains non-'report--' or 'intrusion-set--' entries.
    *   Return False otherwise.
*   Implement `is_atom42_main_report(report_obj: dict) -> bool` in `Packs/FeedUnit42v2/Integrations/FeedUnit42v2/FeedUnit42v2.py` to:
    *   Return True if all 'object_refs' start with 'report--' or 'intrusion-set--', and include at least one of each.
    *   Return False otherwise.
*   Add `get_report_object(self, obj_id: str) -> dict` to the Client class in `Packs/FeedUnit42v2/Integrations/FeedUnit42v2/FeedUnit42v2.py`:
    *   Fetch report by ID from cache or API.
    *   Log 'Unit42v2 Feed: Found more then one object for report object {obj_id} skipping' if multiple results are found.
*   Update `parse_reports_and_report_relationships` to produce 2 report indicators from the standard test data.
*   Ensure `fetch_indicators` returns 24 indicators, including the newly classified sub-report.
*   Remove the standalone `get_attack_id_and_value_from_name` from `Packs/FeedUnit42v2/Integrations/FeedUnit42v2/FeedUnit42v2.py` and use `STIX2XSOARParser.get_mitre_attack_id_and_value_from_name`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.