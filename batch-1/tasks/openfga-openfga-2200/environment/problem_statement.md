## Description

The authorization check system currently cannot efficiently (or correctly) evaluate a class of recursive hierarchical permission models where a relation is defined as a union between one or more non-recursive branches involving algebraic operations and a recursive tuple-to-userset traversal back to the same relation on a parent object.

For example, a model where a user has the viewer permission on a document if they qualify via some intersection or exclusion-based rule OR if they have viewer on the document's parent should be evaluated correctly and efficiently. Currently, these patterns do not trigger the fast-path evaluation code, leading to either incorrect results or avoidable performance degradation.

## Expected Behavior

- The type system should be able to classify whether a relation qualifies for the new fast-path: it is a union where the recursive branch is a tuple-to-userset pointing back to the same relation and every non-recursive branch has weight 1.
- The checker should invoke the new fast-path algorithm when the model qualifies.
- End-to-end authorization checks against these models must return the correct result — including models with wildcards, multiple non-recursive union branches, and non-recursive branches that are parenthesized intersections.

## Why This Matters

Authorization models that propagate access through object hierarchies while also allowing direct access via compound criteria are common in practice. Without this support, users either cannot model these patterns efficiently or get incorrect check results, undermining the reliability and performance of the authorization system.
