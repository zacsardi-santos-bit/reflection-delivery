I'm using a schema import tool that lets me split my GraphQL schema across multiple files using import comments. I have a "master schema" pattern where the root file contains only import directives and no type definitions — it just pulls in the query types from a second file. That second file imports a type from a third file, and the third file imports everything from a fourth file.

When I resolve the full schema starting from the root file, some types that are defined deep in the chain are missing from the output. It seems like transitive dependencies are not being followed all the way through when the entry point is a pure import file with no definitions of its own. All seven types across the four levels should appear in the final schema, but only a subset of them do.

Can this transitive import resolution be fixed so that a master schema file (containing only imports, no type definitions) correctly includes all types reachable through chains of three or more levels of imports?
