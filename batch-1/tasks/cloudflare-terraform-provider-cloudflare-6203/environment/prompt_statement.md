I'm working on the Terraform provider migration tool for Cloudflare configurations and have run into several issues that need to be fixed.

First, when I run the migration tool on load balancer resources that have routing rules containing nested override blocks, the tool corrupts or drops the regional pool assignments inside those overrides. A typical configuration has rules that each contain an overrides block, and each overrides block can have multiple separate pool groupings (by region, by country, by PoP). After migration, the nested pool entries are lost or only one of them is kept, even though there should be several distinct entries preserved. The transformation needs to be applied recursively — nested blocks should be converted just like top-level ones.

Second, the output format for lists of objects is wrong. The tool is generating an expanded format with opening brackets on their own lines and trailing commas, but the expected format is compact with the opening brace on the same line as the bracket.

Third, the load balancer pool origin header attribute migration is incorrect. The tool should produce a simplified single-key format, but it's currently outputting an older intermediate format. This applies whether the origins are defined statically or dynamically through iteration.

Finally, three edge cases in the load balancer rules transformation are not handled: configurations where regional pools already contain the region as a list should be passed through unchanged; an empty rules list should be preserved as-is without errors; and rules that don't have any overrides sub-block should also be preserved without errors. These cases were known to be broken and had their tests disabled, but they need to work correctly now.
