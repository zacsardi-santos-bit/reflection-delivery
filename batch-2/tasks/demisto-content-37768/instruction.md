Update the `advisory_to_indicator` function to correctly parse and convert security advisory data from the updated Palo Alto Networks API schema into threat intelligence indicators. Ensure that all fields are extracted from their new locations in the response.

*   Implement the `advisory_to_indicator` function in `Packs/PaloAltoNetworks_SecurityAdvisories/Integrations/PaloAltoNetworksSecurityAdvisories/PaloAltoNetworksSecurityAdvisories.py`:
    *   Accept a dictionary `advisory_dict` in the CVE v5.0 schema format.
    *   Return a dictionary with keys: 'value', 'type', 'rawJSON', and 'fields'.
    *   Set 'value' to the CVE identifier from `advisory_dict.get('cveMetadata', {}).get('cveId')`.
    *   Set 'type' to 'CVE'.
    *   Include the original `advisory_dict` in 'rawJSON'.

*   Populate the 'fields' dictionary with:
    *   'tags': Extract a list of CWE ID strings from `containers.cna.problemTypes[].descriptions[].cweId`.
    *   'publications': Extract a list of dictionaries, each containing only a 'link' key from `containers.cna.references[].url`.
    *   'cvss' and 'cvssscore': Extract the base score from `containers.cna.metrics[0].cvssV4_0.baseScore`.
    *   'cvssvector': Extract from `containers.cna.metrics[0].cvssV4_0.vectorString`.
    *   'sourceoriginalseverity': Extract from `containers.cna.metrics[0].cvssV4_0.baseSeverity`.
    *   'cvedescription' and 'description': Extract from `containers.cna.descriptions[0].value`.
    *   'published': Extract from `containers.cna.datePublic`.
    *   'name': Extract from `containers.cna.title`.
    *   'cvssversion' and 'cvsstable': Include if CVSS version is '3.1' or '4.0' from `containers.cna.metrics[0].cvssV4_0`.

*   Update the test data file `test_data/advisories4.json`:
    *   Ensure it contains a 'response' key with an advisory in CVE v5.0 format, where `cveMetadata.cveId` is 'CVE-2024-0012'.
    *   Include an 'excepted_response'.'fields' key reflecting the expected output from `advisory_to_indicator`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.