I'm working on adding documentation snippets for Google Cloud Bigtable in Python, and I need to create example code showing different ways to read and filter data.

For the filtering examples, I need to cover the various filter types that Bigtable supports. This includes limiting filters like row sampling, row key regex matching, limiting cells per column or per row, column family and qualifier regex filtering, column ranges, value ranges and regex, timestamp ranges, and the basic block-all and pass-all filters. I also need modifying filters that can strip values or apply labels to cells, plus composing filters that demonstrate chaining filters together with AND logic, interleaving with OR logic, and conditional filtering.

For the read operations, I need examples showing how to read a single row, read partial row data with specific columns, read multiple rows by their keys, read row ranges, read by key prefix, and read with filters applied.

Each snippet should connect to Bigtable using the project, instance, and table identifiers, apply the appropriate filter or read operation, and print the results in a readable format that shows the row key, column family, column qualifiers, values, timestamps, and any labels.
