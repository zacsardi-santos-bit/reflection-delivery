I use a markdown query tool to select and extract content from markdown documents, and I'd like it to support a plain text output mode. Right now it can output results as formatted markdown or as structured data, but neither option is great when I want to pipe the results into other text-processing tools that don't understand markdown syntax.

What I need is a mode where all the markdown formatting is stripped away — things like emphasis markers, link syntax, and code fence markers should all be removed. Links should just show their display text, not the URL or bracket syntax. Code blocks should output just the code content itself, without any fencing or language label. Tables should become simple rows of space-separated values. When there are multiple blank lines between blocks, they should be collapsed down to a single blank line.

I'd also like the existing structured output format to fully cover code blocks, making sure that when a code block is serialized, the code content, language, and block type are all included.

Additionally, the project's test utilities should include a helper for converting a document element to its reference form and verifying that the conversion produced the expected variant — this is useful when writing unit tests for the new output mode.
