Implement the logic to handle certificate ARNs in the AWS Load Balancer Controller's ingress configuration. Ensure that the same certificate can appear as both a default and an SNI certificate, and filter out any empty entries in the certificate list.

*   Filter out empty entries in the certificate-arn annotation:
    *   Ignore consecutive or trailing commas in the annotation value so that they do not appear in the resulting listener's certificate list.

*   Preserve duplicate certificate ARNs:
    *   Allow the same certificate ARN to appear as both the first (default) certificate and as a later SNI certificate in the list.
    *   Ensure the default certificate is not included in the deduplication set used for SNI certificates.

*   Configure listener certificates:
    *   Set the first certificate in the annotation list as the default listener certificate.
    *   Add all subsequent certificates, including duplicates of the default, as SNI certificates.

*   Listener configuration:
    *   Do not create a port 80 listener for an internet-facing ingress with only HTTPS (port 443) certificates specified.
    *   Create listener rules on port 443 only when no HTTP listener is present.

*   Reflect mutual authentication configuration in the listener spec:
    *   Include mode and trust store ARN as specified in the annotation.
    *   Support mode 'off' with an empty trust store ARN.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.