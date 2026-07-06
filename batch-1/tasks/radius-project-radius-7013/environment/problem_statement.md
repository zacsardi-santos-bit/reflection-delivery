## Description

Terraform recipe module archives can contain the actual configuration in a subdirectory rather than at the archive root — for example, the main Terraform files might be nested inside a named subfolder within the archive. There is a common convention for specifying such subdirectory paths using a double-slash separator in the template path. 

Currently, the module inspection step during recipe execution ignores this subdirectory specification and always looks at the root directory of the downloaded module. This means that if a recipe's template path points to a submodule within an archive, the inspection will fail or return incorrect results because it looks in the wrong location.

## Expected Behavior

- When a recipe's template path specifies a subdirectory within an archive using the double-slash separator convention, the module inspection logic should extract and use that subdirectory path when loading the Terraform module for analysis.
- Recipes that do not use this convention should continue to work as before.
- The module inspection function should accept the full recipe definition (including both name and template path) so it can correctly determine where within the downloaded module directory to look for the Terraform configuration.

## Why This Matters

Terraform module archives with subdirectory-based layouts are a common pattern. Without support for this convention, users cannot define Terraform recipes that organize configuration in subdirectories, limiting the flexibility of recipe packaging.
