Implement new security checks for AWS resources in Terraform configurations using YAML-based graph policy checks. Ensure these checks detect specific security misconfigurations and are automatically discoverable by the test framework.

*   Implement a graph-based policy check for API Gateway stages:
    *   Evaluate `aws_api_gateway_stage` (v1) and `aws_apigatewayv2_stage` (v2) resource types.
    *   Pass if `client_certificate_id` attribute is present; fail if absent.
    *   File: `APIGatewayEndpointsUsesCertificateForAuthentication.yaml`
    *   Location: `checkov/terraform/checks/graph_checks/aws/`

*   Implement a graph-based policy check for API Gateway methods:
    *   Evaluate `aws_api_gateway_method` resources.
    *   Pass if `request_validator_id` attribute is present; fail if absent.
    *   File: `APIGatewayRequestParameterValidationEnabled.yaml`
    *   Location: `checkov/terraform/checks/graph_checks/aws/`

*   Implement a graph-based policy check for CloudFront distributions:
    *   Evaluate `aws_cloudfront_distribution` resources.
    *   Pass if no custom origin configuration is present or if `ssl_protocols` does not include `SSLv3`.
    *   Fail if `custom_origin_config.origin_ssl_protocols` contains `SSLv3`.
    *   File: `CloudFrontUsesSecureProtocolsForHTTPS.yaml`
    *   Location: `checkov/terraform/checks/graph_checks/aws/`

*   Implement a graph-based policy check for EMR clusters:
    *   Evaluate `aws_emr_cluster` resources.
    *   Pass if `security_configuration` attribute is present; fail if absent.
    *   File: `EMRClusterHasSecurityConfiguration.yaml`
    *   Location: `checkov/terraform/checks/graph_checks/aws/`

*   Implement a graph-based policy check for OpenSearch and Elasticsearch domains:
    *   Evaluate `aws_opensearch_domain` and `aws_elasticsearch_domain` resources.
    *   Pass only if both `advanced_security_options.enabled` and `advanced_security_options.internal_user_database_enabled` are true.
    *   Fail if either is false or absent.
    *   File: `OpenSearchDomainHasFineGrainedControl.yaml`
    *   Location: `checkov/terraform/checks/graph_checks/aws/`

*   Each YAML file must include:
    *   A `metadata` section with `id`, `name`, and `category`.
    *   A `definition` section with conditions using `cond_type`, `resource_types`, `attribute`, `operator`, and optionally `value`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.