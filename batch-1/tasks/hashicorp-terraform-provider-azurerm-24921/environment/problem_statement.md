## Description

When managing Azure Data Factory pipelines through Terraform, users encounter failures when their pipeline contains a web activity with HTTP headers that use Data Factory's expression syntax. In the Azure Data Factory API, a header value can be either a plain string or a structured expression object (for dynamic values). However, the Azure SDK client used by the Terraform provider only supports one of these formats, causing Terraform to error out when it reads back a pipeline that was created with expression-typed headers.

## Expected Behavior

- Terraform should be able to create, read, update, and import Data Factory pipelines containing web activities whose HTTP headers include both plain string values and structured expression objects.
- Pipelines with mixed header value types should round-trip successfully — what is applied should be readable afterward without errors.

## Current Behavior

When a pipeline activity includes a web activity header using expression syntax (e.g., a header value that is a JSON object rather than a plain string), Terraform fails to read the pipeline resource after creation. This makes it impossible to manage such pipelines via Terraform without encountering errors.

## Why This Matters

Azure Data Factory expressions are a core feature that users rely on to build dynamic pipelines. Blocking expression-typed headers in web activities prevents users from adopting Terraform to manage a meaningful class of production pipelines, leaving them unable to use infrastructure-as-code for those resources.
