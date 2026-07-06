## Description

There are several gaps in the changelog and hosting service integrations that need to be addressed:

1. **Missing issue URL generation for some platforms**: Changelog templates can reference pull requests and commits, but GitHub and Gitea don't support generating direct links to issues. GitLab already supports this but the other platforms are missing it.

2. **Ambiguous release asset upload method name**: The method for uploading files to a release has a generic name that doesn't make clear it's specifically for release artifacts. Renaming it would make the API more self-documenting and consistent.

3. **Bitbucket on-premises not supported**: The Bitbucket integration only works with the cloud version. Enterprise users with self-hosted Bitbucket Server installations cannot configure a custom domain, subdomain, path prefix, or HTTP (insecure) connection. These configurations all fail or produce incorrect API URLs.

4. **Base hosting interface can be instantiated directly**: The base class for hosting service clients is supposed to be abstract — all concrete functionality lives in the subclasses. However, it can currently be instantiated directly, which bypasses required method implementations. It should enforce that all subclasses provide the mandatory methods.

## Expected Behavior

- GitHub and Gitea should each expose a method to generate links to issues by issue number, accepting both integer and string inputs
- The method for uploading release attachments should be named to clearly indicate it handles release assets specifically
- Bitbucket should work correctly when pointed at a custom domain or self-hosted server, including configurations with subdomain-based APIs, path-based APIs, and insecure HTTP connections
- The base hosting service class should be abstract and raise an error if someone tries to instantiate it without providing a concrete subclass implementation
- Changelog templates should be able to use all these URL-generation capabilities as template filters, with the specific filters available depending on which hosting service is configured

## Why This Matters

Teams using self-hosted Bitbucket Server are currently unable to use the tool at all. Projects that track work via issues on GitHub or Gitea cannot generate useful issue links in changelogs. The inconsistent method naming makes the public API harder to understand and use correctly.
