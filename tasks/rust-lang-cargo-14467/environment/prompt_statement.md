I'm working with the experimental namespaced package names feature in Cargo, where package names can contain double-colon separators. I've run into a problem where certain commands don't recognize namespaced package names as valid specifications.

When I try to update a namespaced package by specifying its name directly, the command fails — it seems like the parser is misinterpreting the double-colon separator in the name as part of a version specifier. The same failure happens when I provide the full qualified package identifier, which embeds the namespaced name in its path fragment.

I'd expect both forms to be accepted: using just the namespaced name as the update target, and using the full package identifier that includes the namespaced name. In either case, when no actual update is needed, the command should report that zero packages were updated, with no output on standard out.

Additionally, the command for displaying a package's own identifier should correctly output the full qualified package ID, including the namespaced name with its double-colon separator intact, in the standard identifier format.

Could you fix the package specification parser so it correctly distinguishes between double-colon namespace separators in package names and the single-colon or at-sign separators used before version numbers?
