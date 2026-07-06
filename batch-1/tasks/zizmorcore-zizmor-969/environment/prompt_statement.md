I'm working on improving the obfuscation detection in a GitHub Actions security linting tool. There are two things I'd like to fix.

First, the diagnostics currently highlight the entire expression including the surrounding delimiter characters, when they should be pointing at just the inner content. For example, if a constant expression is embedded in a template, the reported location and span should refer to the constant part itself, not the whole template placeholder. The messages could also be shorter and more direct.

Second, there's a missing detection case: when a dynamic, computed value is used as an index key to look up data in an object or array, this can obscure what is actually being accessed at runtime. I'd like this to be detected and flagged — specifically when running in the most thorough analysis mode. The finding should point to the index access bracket and the computed key, and report that the index expression is computed rather than a literal value.

The test data would be a GitHub Actions workflow that uses an expression like accessing a collection with a context-derived key, so the detection can be verified end to end.
