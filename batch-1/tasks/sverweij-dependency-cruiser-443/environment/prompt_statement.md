I'm trying to write a reachability rule in dependency-cruiser that uses a regex capture group in the "from" pattern to capture a folder name, and then references that captured value in the "to" pattern to scope the rule to just that folder's subtree. The idea is to have a single rule that checks, for each module's index file, whether all modules in that module's folder are reachable from it.

The problem is that capture groups in the "from" pattern don't seem to get expanded into the "to" pattern when computing reachability. The rule ends up matching too broadly or not at all, instead of being evaluated independently per matching "from" module with the appropriate captured value substituted in.

Beyond the capture group issue, I'd also like each reachability annotation on a module to record which specific "from" module triggered it — not just which rule. Right now, if multiple source modules can match the "from" side of a rule, there's no way to tell which one caused a particular module to be marked reachable or unreachable. Having that "matched from" information would also be necessary for the capture group expansion to work correctly during validation.

Could you implement support for recording the originating "from" module path in each reachability annotation, and use that information to correctly expand capture groups when both computing reachability and validating rules?
