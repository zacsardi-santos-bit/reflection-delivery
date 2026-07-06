I'm cleaning up the markdown formatter and fenced code blocks are giving me grief. A few things are off and I want them all fixed so our output matches what mainstream markdown formatters produce, since right now we diverge from common conventions and create noisy diffs when files mix styles.

First problem, when a code block uses tilde characters as the fence delimiter (four or more tildes) instead of backticks, the formatter just leaves the tildes as-is. I want it to always normalize to backtick-style fences for consistency, so tilde-delimited blocks get reformatted to backticks.

Second, the info string handling is broken in a few ways. That's the language or metadata tag written right after the opening fence. Right now it inserts a spurious leading space between the fence characters and the info string when there shouldn't be one, so the info string needs to sit immediately after the fence with no leading space. Also it doesn't strip trailing whitespace off the info string, and I want that trimmed.

Oh and the big one, info strings that use comma-separated or multi-value formats (which are super common in documentation toolchains) aren't parsed or formatted correctly, so their content gets garbled or silently dropped. That matters because some tools rely on that metadata, so I need those comma-separated info strings parsed and preserved properly through the reformat.

So to sum up what I'm after, normalize all fenced code blocks to backtick delimiters, write the info string right after the fence with trailing whitespace stripped and no leading space, and handle comma-separated or multi-value info strings without losing anything.
