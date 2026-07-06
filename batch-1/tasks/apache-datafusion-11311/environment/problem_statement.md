## Description

The Substrait query plan consumer in DataFusion does not currently support existence-check subqueries. When a Substrait plan contains a filter that uses an "does at least one matching row exist?" predicate — a very common pattern in analytical SQL — the consumer crashes with a "not implemented" error instead of translating it into the corresponding DataFusion query plan node.

This blocks a significant class of queries from being processed through the Substrait interface, including several standard TPC-H benchmark queries. For example, TPC-H Query 4 filters orders based on whether any matching line items have a late delivery (commit date after receipt date), which uses exactly this existence-check pattern.

## Expected Behavior

- When given a Substrait plan that uses an existence-check subquery as part of a filter condition, the consumer should successfully translate it into a DataFusion logical plan where the existence check is represented as a proper subquery expression.
- The translated plan for a query like TPC-H Q4 (order priority checking with a correlated existence filter over line items) should reflect the correct plan structure: projection, sort, aggregation, filtered table scan with the existence check, and the inner subquery relation.
- The translated plan for a query like TPC-H Q5 (local supplier volume with multiple joined tables, date range filters, and computed revenue) should also work correctly end-to-end.

## Why This Matters

Substrait is meant to be a universal, cross-engine query plan interchange format. Failing to support a basic SQL pattern like "filter by existence of related rows" means many real-world analytical queries cannot be round-tripped through DataFusion via Substrait, limiting interoperability with other systems that produce Substrait plans.
