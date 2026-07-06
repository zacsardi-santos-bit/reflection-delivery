## Description

Schemathesis already interprets 4xx validation error responses from many popular frameworks — including frameworks from the Python, JavaScript, PHP, Ruby, and .NET ecosystems — to learn what constraints an API enforces on its inputs and use that knowledge to generate smarter test cases. However, support is missing for the Go validation ecosystem.

When a Go-based API returns a validation error because a request field was too short, had the wrong format, fell outside a numeric range, or contained a value not in an allowed set, Schemathesis cannot interpret those errors. It cannot refine its test generation and may repeatedly send requests that violate the same constraint, reducing the chance of finding real bugs.

## Expected Behavior

- When an API returns a validation error in the compact, text-based format common to the Go validation ecosystem, Schemathesis should recognise the envelope and extract constraints for fields that are self-describing (e.g. required fields, format constraints like email, URL, or UUID).
- When an API returns a validation error in the structured JSON format used by the same ecosystem, Schemathesis should extract the full set of constraints including string length bounds, numeric range bounds (inclusive and exclusive), exact-length requirements, enumerated allowed values, date/datetime format expectations, and required field markers.
- Constraint information should be mapped to the correct parameter path, correctly handling nested structs and array element references.
- Observations derived from Go validation errors should not be incorrectly attributed to other existing parsers; all other parsers should decline to handle go-validator-shaped responses.
- For read operations (GET), constraints should be attributed to query parameters rather than a request body.

## Why This Matters

Without this support, Schemathesis repeatedly generates inputs that violate Go-specific validation rules, masking real bugs behind false positives and reducing test effectiveness on Go services. Adding this parser brings Go APIs to the same level of coverage as other supported frameworks.
