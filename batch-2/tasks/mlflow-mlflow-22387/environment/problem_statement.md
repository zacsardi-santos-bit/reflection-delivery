I'm knee-deep in the multi-workspace stuff and hit a cluster of routing/navigation bugs I want to fix in one pass.

First off the workspace router is way too trigger-happy about redirecting people away when the URL has a workspace identifier in it. If the workspace list is still loading, or it errored out, or it just doesn't happen to include that workspace, the router keeps bouncing users away and it's a mess. I want the workspace name from the URL trusted as-is and set as the active workspace, no navigation redirect at all, regardless of whether the list loaded or returned an error.

Second, the workspace selector dropdown only lets you pick from what the server returned. I want folks to type a workspace name directly and go there even if it's not in the list. So the selector should surface a "Go to workspace" option for any valid, non-matching typed name, trim the surrounding whitespace, and actually navigate there when picked. This needs to work even when the list totally fails to load.

Third, the home page should notice when the currently selected workspace doesn't exist on the server anymore. Right now it silently fails or shows some generic error. Instead I want a clear "Page Not Found" page that names the missing workspace, includes a link back to the plain home route (not the workspace-prefixed one), and also clears that stale workspace out of the local remembered state.

Fourth, the error view component that renders those fallback nav links should take an option to skip workspace URL prefixing on the link, for cases like this not-found scenario where it should point at the root.

Oh and lastly, tighten up workspace name validation to also reject names that are too short (under 2 characters) or too long (64 or more characters), which currently slip through client-side validation.

Why it all matters: right now users get stuck in redirect loops, can't reach workspaces they know exist, and see stale workspace context after one's been removed.
