Implement support for the ASOF JOIN syntax in the Snowflake SQL dialect of SQLFluff. Ensure that the parser can recognize and correctly parse this join type, including its specific time-matching condition clause and optional ON condition. This will enable SQLFluff to lint and process Snowflake SQL queries using this feature without errors.

*   Update the Snowflake SQL dialect to recognize 'ASOF' as a valid join type keyword.
*   Implement parsing logic for the MATCH_CONDITION clause:
    *   Recognize MATCH_CONDITION as a keyword following the joined table reference in an ASOF JOIN.
    *   Parse the MATCH_CONDITION clause as a 'match_condition' node in the parse tree.
    *   Support bracketed comparison expressions within MATCH_CONDITION using operators >=, >, and <.
*   Allow an optional ON condition to follow the 'match_condition' node, parsed as 'join_on_condition'.
*   Ensure ASOF JOINs can be chained in a single FROM clause and parse correctly.
*   Support mixing ASOF JOINs with standard join types (e.g., INNER JOIN) in the same query.
*   Add 'ASOF' and 'MATCH_CONDITION' to the list of recognized Snowflake keywords.
*   Ensure that queries using ASOF JOIN parse without errors and are processable by the linter/fixer.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.