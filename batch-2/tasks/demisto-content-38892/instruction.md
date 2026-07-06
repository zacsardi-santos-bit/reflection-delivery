Implement a new threat intelligence feed integration for Google Threat Intelligence that ingests threat indicators into your security platform. Handle four types of indicators: files, domains, URLs, and IP addresses, enriching each with specific metadata. Support both manual and automated retrieval of indicators and ensure the integration is properly configured with a connectivity test.

*   Extend the `Client` class from `BaseClient`:
    *   Accept a `base_url` string.
    *   Implement `get_threat_list(feed_type: str, package: str, filter_query: str = None, limit: int = 10) -> dict` to return a dictionary with an 'iocs' key containing a list of items.

*   Implement `fetch_indicators_command`:
    *   Process each item by extracting `item.get('data', {})`.
    *   For URL-type items, use `attributes.url` as the indicator value; for others, use the item id.
    *   Determine indicator type from the 'type' field and map to `FeedIndicatorType`.
    *   Map verdicts to scores: 'VERDICT_MALICIOUS' -> 3, 'VERDICT_BENIGN' -> 1, 'VERDICT_SUSPICIOUS' -> 2, others -> 0.

*   Ensure indicator fields are correctly populated:
    *   File indicators: Include fields like `md5`, `sha1`, `sha256`, `ssdeep`, `fileextension`, `filetype`, `imphash`, `displayname`, `name`, `size`, `creationdate`, `firstseenbysource`, `lastseenbysource`, `updateddate`, `tags`, `detectionengines`, `positivedetections`, `gtithreatscore`, `gtiseverity`, `gtiverdict`, `actor`, `malwarefamily`.
    *   Domain indicators: Include fields like `admincountry`, `adminname`, `adminemail`, `adminphone`, `registrantcountry`, `registrantemail`, `registrantname`, `registrantphone`, `registrarabusephone`, `registrarabuseemail`, `registrarname`, `firstseenbysource`, `lastseenbysource`, `creationdate`, `updateddate`, `tags`, `detectionengines`, `positivedetections`, `gtithreatscore`, `gtiseverity`, `gtiverdict`, `actor`, `malwarefamily`.
    *   URL indicators: Include fields like `tags`, `firstseenbysource`, `lastseenbysource`, `updateddate`, `detectionengines`, `positivedetections`, `gtithreatscore`, `gtiseverity`, `gtiverdict`, `actor`, `malwarefamily`.
    *   IP indicators: Include fields like `tags`, `firstseenbysource`, `lastseenbysource`, `updateddate`, `countrycode`, `detectionengines`, `positivedetections`, `gtithreatscore`, `gtiseverity`, `gtiverdict`, `actor`, `malwarefamily`.

*   Implement `get_indicators_command`:
    *   Return a `CommandResults` object with `raw_response` as the list of indicator dicts.
    *   Use `params` for `tlp_color` and `feedTags`, and `args` for `feed_type`, `package`, `filter`, and `limit`.

*   Implement the `main()` function:
    *   Handle 'gti-threatlists-get-indicators' by reading from `demisto.args()`, calling `get_indicators_command`, and outputting via `demisto.results()`.
    *   Handle 'fetch-indicators' by reading from `demisto.params()`, calling `fetch_indicators_command`, and using `demisto.createIndicators()`.
    *   Handle 'test-module' by calling `get_threat_list` once.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.