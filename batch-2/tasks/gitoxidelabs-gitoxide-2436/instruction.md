I'm working on the refspec prefix logic and noticed an issue with how exact refs are handled when communicating with a remote.

*   The prefix() method on RefSpecRef must return the full ref string unchanged for exact, non-glob refs that start with 'refs/', regardless of how many path components they contain. For example, 'refs/heads/main' must return 'refs/heads/main', 'refs/foo/bar' must return 'refs/foo/bar', 'refs/namespaces/foo/refs/heads/main' must return 'refs/namespaces/foo/refs/heads/main', and 'refs/short' (only one component after 'refs/') must return 'refs/short'.

*   The prefix() method on RefSpecRef must return only the portion of the ref up to (but not including) the wildcard character for simple single-wildcard glob patterns. For example, 'refs/heads/*:refs/remotes/origin/*' must return 'refs/heads/', and 'refs/namespaces/*:refs/remotes/origin/*' must return 'refs/namespaces/'.

*   The prefix() method on RefSpecRef must return None for complex glob patterns: patterns where the wildcard appears immediately after 'refs/' (e.g., 'refs/*/main'), patterns with more than one wildcard character (e.g., 'refs/*/foo/*'), and patterns containing any of the special characters '?', '[', ']', or '\'.

*   For push refspecs, the prefix() method must use the destination side of the refspec (not the source). For example, a push spec of 'refs/local/main:refs/remote/main' must return 'refs/remote/main'.

*   The expand_prefixes() method must produce results consistent with the updated prefix() behavior: exact refs expand to the full ref string, simple single-wildcard patterns expand to the prefix up to the wildcard, and complex patterns or patterns matching none of the supported forms expand to an empty collection.

*   The 'no mapping' detection during a fetch must trigger an error not only when the server advertises refs but none match the refspecs, but ALSO when an explicit exact-ref (non-wildcard) fetch refspec was given and the server returned zero remote refs. When triggered, the error message must be: 'None of the refspec(s) {refspec} matched any of the {count} refs on the remote', where {count} is the number of remote refs actually returned (0 when none were returned).

*   When fetching with a specific non-glob refspec such as 'refs/heads/main', the server-side ls-refs query must use the exact full ref name as the prefix filter, so only refs matching that exact prefix are advertised (e.g., 'refs/heads/dev' must not appear in the results when fetching 'refs/heads/main').


*   Interface details: Type: Method
Name: prefix
Location: gix-refspec/src/spec.rs
Signature: prefix(&self) -> Option<&BStr>
Description: Returns the ref prefix for this refspec, used to filter refs advertised by a remote server during ls-refs. For exact non-glob refs starting with "refs/" (e.g., "refs/heads/main", "refs/short", "refs/namespaces/foo/refs/heads/main"), returns the full ref unchanged as the prefix. For simple single-wildcard glob refspecs (e.g., "refs/heads/*:refs/remotes/origin/*"), returns the portion of the path before the wildcard character (e.g., "refs/heads/"). Returns None for complex patterns: those with a wildcard appearing immediately after "refs/" (star at position 0 of the suffix), patterns containing more than one wildcard, or patterns containing any of '?', '[', ']', or '\\'. For push specs, uses the destination side; for fetch specs, uses the source side. This method belongs to the RefSpecRef struct in gix-refspec/src/spec.rs.

Type: Method
Name: expand_prefixes
Location: gix-refspec/src/spec.rs
Description: Expands the refspec into one or more ref prefixes, placing them into an output buffer (Vec<BString>). The behavior is consistent with prefix(): exact non-glob refs expand to the full ref string; simple single-wildcard patterns expand to the prefix before the wildcard; complex patterns or patterns without any refs/ prefix expand to nothing (output is left empty). This method belongs to the RefSpecRef struct.

Type: Check (no new function required, but existing logic must be changed)
Name: "no mapping" detection
Location: gix/src/remote/connection/fetch/receive_pack.rs
Description: The condition that decides whether to return a "no mapping" error during fetch must be updated. Previously the condition was: mappings is empty AND remote_refs is non-empty. This must be extended to also trigger when an explicit, non-wildcard fetch refspec was provided but no remote refs were returned by the server (i.e., remote_refs is empty because the exact ref does not exist). Any correct detection of "explicit exact-ref refspec with no corresponding remote ref" is acceptable. The error message format must remain: "None of the refspec(s) {refspecs} matched any of the {count} refs on the remote", where {count} will be 0 when the server returns no refs.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.