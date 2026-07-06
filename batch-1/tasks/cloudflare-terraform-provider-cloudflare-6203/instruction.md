Implement the necessary fixes in the Cloudflare Terraform provider migration tool to address issues with load balancer configurations. Ensure that nested block transformations are complete, multiple instances of block types are preserved, and edge cases are handled correctly. Update the output format for lists of objects and correct the load balancer pool origin header format.

*   Ensure the HCL transformer applies transformations recursively to nested blocks within list items.
    *   Transform any nested blocks within each list item listed in the transformation config.
    *   Preserve the full nested structure during transformation.

*   Preserve multiple instances of the same nested block type within a parent block.
    *   Ensure each instance (e.g., region_pools blocks) is preserved as a separate entry.
    *   Avoid merging or deduplicating these blocks.

*   Use compact syntax for the list-of-objects output format.
    *   Begin lists with '[{' and separate successive objects with ', {' or '}, {' on the same line.
    *   Ensure attribute values within objects have no trailing commas and lists end with '}]'.

*   Transform the load balancer pool origin header attribute to the compact format.
    *   Convert from 'header = "Host"' and 'values = [...]' to 'host = [...]'.
    *   Apply this transformation to both statically defined origins and dynamically generated origins using for_each expressions.

*   Ensure header pattern detection tolerates whitespace and newline variations.

*   Handle load balancer rules transformation for specific edge cases.
    *   Pass through rules with 'region' as a list inside region_pools without re-transforming them.
    *   Preserve an empty rules list (rules = []) as an empty list in the output without error.
    *   Preserve rules blocks with no overrides sub-block, maintaining all rule attributes as-is.

*   Preserve all resource reference expressions verbatim in the transformed output.

*   Implement the TransformFile method in HCLTransformer.
    *   Accept an input .tf file path and an output file path.
    *   Apply all configured transformations and write the result to the output path.
    *   Handle nested block transformations recursively and use compact list formatting.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.