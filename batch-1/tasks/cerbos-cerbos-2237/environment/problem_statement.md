## Description

When writing access control policies in Cerbos, authors use condition expressions to evaluate properties of the incoming request — for example, checking attributes of the requesting principal or the target resource. Currently, two contextual properties that are part of every authorization request — the **scope** and **policy version** — associated with both the principal and the resource, are not accessible in these condition expressions.

This means it is not possible to write policy rules such as "allow access only if the principal and the resource are in the same scope" or "allow access only if the principal and resource use the same policy version," even though both values are already submitted as part of every access check request.

## Expected Behavior

- The scope of the requesting principal should be accessible in policy condition expressions
- The policy version of the requesting principal should be accessible in policy condition expressions
- The scope of the target resource should be accessible in policy condition expressions
- The policy version of the target resource should be accessible in policy condition expressions
- Policy rules that condition access on equality or comparison of these values (across principal and resource, or against known constants) should evaluate correctly

## Why This Matters

Without access to these values in condition expressions, policy authors cannot write fine-grained, context-aware authorization rules that take scope or policy version into account. These fields are part of the request contract and should be available alongside other already-accessible request properties.
