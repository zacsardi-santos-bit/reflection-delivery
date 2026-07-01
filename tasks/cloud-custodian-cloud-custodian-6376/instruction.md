Implement support for API Gateway client certificates in the cloud governance framework. Enable listing and filtering of API Gateway client certificates and stages based on certificate properties, particularly focusing on expiration status.

*   Implement a new resource type 'rest-client-certificate' in `c7n/resources/apigw.py`.
    *   Register this resource type under the name 'rest-client-certificate'.
    *   Ensure it calls the GetClientCertificates API to return all client certificate resources.
    *   Include fields: clientCertificateId, description, pemEncodedCertificate, createdDate, expirationDate.
    *   Register in `c7n/resources/resource_map.py` under 'aws.rest-client-certificate'.
    *   Add to the resource ARN whitelist_missing set in tests.

*   Implement a filter type 'client-certificate' for the 'rest-stage' resource in `c7n/resources/apigw.py`.
    *   Register this filter under the name 'client-certificate' in the 'rest-stage' resource's filter registry.
    *   Support filtering by certificate fields such as 'expirationDate' using standard value-based options:
        *   'key' parameter (e.g., 'expirationDate')
        *   'value_type' (e.g., 'expiration')
        *   'value' and 'op' (e.g., 'lte')
    *   Annotate matched 'rest-stage' resources with 'c7n:matched-client-certificate', including relevant certificate fields.
    *   Ensure compatibility with both 'describe' (direct API) and 'config' (AWS Config) source modes.
        *   In 'config' mode, read clientCertificateId from 'ClientCertificateId' in the configuration and fetch certificate data via the API.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.