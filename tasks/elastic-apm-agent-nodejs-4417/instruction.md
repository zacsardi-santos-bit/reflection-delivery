Remove the deprecated HTTP client library from the Node.js APM agent project and update all test files to use the built-in HTTP module. Ensure the tests continue to function correctly by utilizing a shared helper utility for form-encoded POST requests.

*   Remove the 'request' library from the `devDependencies` section of `package.json`.
*   Update `package-lock.json` to eliminate the 'request' package and its transitive dependencies:
    *   aws-sign2, aws4, caseless, extend, forever-agent, form-data, har-schema, har-validator, http-signature, is-typedarray, isstream, jsprim, oauth-sign, performance-now, psl, tough-cookie, tunnel-agent.
*   Ensure that after running `npm ci`, the 'request' package is not present in `node_modules`.
*   Update all test files in `test/sanitize-field-names/` (express, fastify, hapi, koa, restify) to use the shared form-POST helper function for sending form-encoded POST requests.
*   Verify that cloud metadata test servers respond correctly:
    *   AWS endpoint returns JSON with a truthy 'version' field.
    *   GCP endpoint returns JSON with a truthy 'instance.id' field.
    *   Azure endpoint returns JSON with a truthy 'compute.vmId' field.
*   Ensure the AWS IMDSv2 token endpoint returns the exact string 'AQAAAOaONNcThIsIsAfAkEtOkEn_b94UPLuLYRThIsIsAfAkEtOkEn==' for a PUT request with the correct TTL header.
*   Confirm that subsequent metadata requests using the AWS token return JSON with a truthy 'version' field.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.