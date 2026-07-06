## Description

When an ALB ingress is configured with multiple TLS certificates, the controller should allow the same certificate to appear in both the "default" role and the "SNI" role. However, the current deduplication logic silently removes any certificate that was already seen — including the first (default) certificate — so if a user explicitly lists the same certificate again later in the annotation (or if an IngressGroup member contributes the same certificate that another member already set as the default), the second occurrence is dropped.

This means the resulting listener's certificate list does not match what the user specified. When AWS ALB allows a certificate to serve both as the default and as an SNI entry, the controller should honor that instead of suppressing it.

Additionally, empty certificate ARN entries that arise from trailing or double commas in the annotation value should be silently filtered out rather than causing unexpected behavior.

## Expected Behavior

- If the same certificate ARN appears as the first (default) certificate **and** is also listed again later in the annotation, the controller must preserve both occurrences: one as the default certificate and one as an SNI certificate.
- Empty entries in the comma-separated certificate list are filtered out and do not appear in the final listener configuration.

## Why This Matters

Users combining ingresses into a group — or simply re-specifying the same certificate for clarity — should not have their configuration silently altered. The controller must faithfully translate the certificate list into the corresponding ALB listener configuration, including duplicate entries that are intentional across default and SNI roles.
