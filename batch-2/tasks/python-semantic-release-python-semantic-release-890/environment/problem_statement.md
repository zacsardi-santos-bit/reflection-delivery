I'm cleaning up our changelog generation and the hosting service integrations and hit a cluster of related gaps I want fixed together.

First off, our changelog templates can already link to pull requests and commits, and GitLab can build issue links, but GitHub and Gitea have no way to generate a direct URL to an issue by its number. I want an issue-link method on both of those hosting clients that accepts the issue number as either an integer or a string and returns the right URL.

Second, the method we use to upload release attachments has too generic a name, it reads like a general file upload. I want it renamed so it's obvious it's specifically for uploading assets to a release, and any internal callers, like the distribution upload logic, need to point at the new name.

Third, Bitbucket only talks to the cloud version right now, so folks running on-prem Bitbucket Server can't aim the tool at their own box. I need it to handle a custom domain with an explicitly provided API path, a custom domain where the API lives on a separate subdomain, a custom domain where the API URL should be derived automatically, path-prefix-based server addresses, and insecure HTTP connections both when the http scheme is spelled out and when it should be inferred from the config. These all either fail today or spit out wrong API URLs.

Finally, the base class every hosting service client inherits from is supposed to be abstract but you can instantiate it directly right now, which skips the required method implementations. I want it to actually be abstract so it raises if someone constructs it without a concrete subclass, forcing subclasses to implement the mandatory interface methods. Oh and it should expose a method that returns all the URL-generation functions the changelog template system registers as filters, where the exact set of filters varies depending on which hosting service is configured (so GitHub/Gitea now surface issue links too).

Teams on self-hosted Bitbucket Server literally can't use the tool today, and projects tracking work as issues on GitHub or Gitea can't get useful issue links in their changelogs, so this matters.
