I'm working on improving the secrets resolution system in the Datadog Agent. There are two main issues I need to fix.

First, the path tracking for secrets inside YAML arrays is broken. Right now, if I have two secrets inside the same array, they both get assigned the same path (e.g., both show up as "instances/password" in debug output), making it impossible to tell them apart. I need the path to include the numeric index of the array position so each secret has a unique, accurate location.

Second, there's no way to refresh secrets after they've been resolved at startup. I need a subscription mechanism where callers can register a callback to be notified whenever a secret is resolved or its value changes. The callback should receive the handle name, origin, full path, and both the old and new values. I also need a refresh operation that re-fetches all known handles from the backend and notifies subscribers only when values actually change. For safety, a per-handle allowlist should restrict which secrets are permitted to change during a refresh.

As part of this work, the configuration method that sets up the secrets backend needs to accept a new parameter for the refresh interval. Additionally, I need a utility function that can assign a value to a config setting by navigating a path of string keys, including into nested maps and slices using numeric string indices.

The existing callback-based resolve method should be replaced entirely by the new subscription model described above.
