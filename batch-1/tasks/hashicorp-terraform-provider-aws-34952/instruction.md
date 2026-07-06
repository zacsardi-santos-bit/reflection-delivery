Implement two new provider-scoped functions within the AWS Terraform provider to handle AWS resource identifiers. The first function should assemble an identifier from its components, and the second should deconstruct an identifier into its components, validating the input format.

*   Implement 'arn_build' function:
    *   Register under 'provider::aws' namespace.
    *   Accept five string parameters: partition, service, region, account ID, and resource.
    *   Return a correctly formatted ARN string.
    *   Example: Inputs ('aws', 'iam', '', '444455556666', 'role/example') should return 'arn:aws:iam::444455556666:role/example'.
    *   Implement in 'internal/function/arn_build_function.go'.
    *   Ensure it is callable as `provider::aws::arn_build` in HCL configurations.

*   Implement 'arn_parse' function:
    *   Register under 'provider::aws' namespace.
    *   Accept a single string parameter containing an ARN.
    *   Return an object with ARN components as attributes.
    *   Validate the input; if the ARN is invalid, return an error with the message 'arn: invalid prefix'.
    *   Implement in 'internal/function/arn_parse_function.go'.
    *   Ensure it is callable as `provider::aws::arn_parse` in HCL configurations.

*   Package requirements:
    *   Implement both functions in the 'internal/function' package.
    *   Include this package in the provider's function registration.
    *   Ensure inclusion in both the unit test script (unit_tests.sh) and the acceptance test script (acceptance_tests.sh).

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.