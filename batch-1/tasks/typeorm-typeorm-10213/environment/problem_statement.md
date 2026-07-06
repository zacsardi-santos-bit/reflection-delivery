## Description

When building queries with multiple WHERE conditions that include OR logic, there's a risk of incorrect boolean evaluation order. For example, combining a strict equality check with a broader OR-based search condition can produce unintended results because the OR conditions are not automatically grouped with parentheses. Without explicit brackets, the database may evaluate the conditions in an unintended order, leading to incorrect query results.

## Expected Behavior

- A data source configuration option should exist that, when enabled, automatically wraps each individual WHERE condition in parentheses.
- When this option is enabled, a query combining a simple equality condition with an OR-based condition should produce SQL where the OR condition is enclosed in parentheses, ensuring correct precedence.
- For example, combining an exact ID equality condition with an OR-based name search should result in SQL where the OR condition is enclosed in parentheses, ensuring the OR is evaluated before the AND, rather than the OR condition being evaluated last due to default operator precedence.

## Why This Matters

Without automatic isolation of WHERE conditions, developers must manually add brackets around every complex OR condition to ensure correct behavior. This is error-prone and easy to forget, especially in large codebases. A global configuration option that automatically applies this isolation ensures safe and predictable query behavior without requiring per-query manual intervention.
