## Description

When building Python distributions (wheels) for StackStorm components, there is significant duplication in the metadata that needs to be specified for each `python_distribution()` target. Common metadata like author information (`author="StackStorm"`, `author_email="info@stackstorm.com"`), project URLs, license information, and standard classifiers must be manually specified for every package, leading to maintenance overhead and potential inconsistencies.

The Pants build system supports a `SetupKwargs` plugin hook that allows auto-generation of setup.py metadata when building distributions. This feature should be leveraged to centralize common StackStorm package metadata.

## Expected Behavior

- A Pants plugin should automatically inject common metadata into all `python_distribution()` targets when they are built
- Common metadata (author, author_email, url, license, project_urls) should be automatically added without manual specification
- The version should be automatically extracted from a specified `version_file` that contains a `__version__` variable
- If a `README.rst` exists in the distribution directory, it should automatically be used as the `long_description`
- Standard classifiers (development status, intended audience, license, operating system, Python versions) should be prepended to any user-specified classifiers
- Users should only need to provide the package name, description, version_file path, and optionally custom classifiers

## Current Behavior

Currently there is no centralized mechanism for managing common package metadata. Each `python_distribution()` target would need to manually specify all metadata fields, leading to:

1. Duplicated metadata across many BUILD files
2. Risk of inconsistent metadata between packages
3. More verbose BUILD files
4. Higher maintenance burden when updating common metadata
