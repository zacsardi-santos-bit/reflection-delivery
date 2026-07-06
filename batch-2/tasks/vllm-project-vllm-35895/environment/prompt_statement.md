I'm hitting a bunch of bugs in the streaming tool call parser for the MiniMax M2 model in vLLM, and they're all messing up inference output for anything that uses tool calls. First problem: when the model generates a natural-language preamble before a tool call block, like "Let me check the weather for you." followed by the actual call, that preamble text just vanishes instead of getting forwarded to the client as normal content. I need that leading text streamed out as content like you'd expect.

Also parallel tool calls are broken. When the model emits two or more function calls inside a single tool call block and multiple complete calls land in the same streaming chunk, only the first one gets emitted and the rest silently disappear. All complete function calls in a block need to come through even when they arrive together in one chunk.

On top of that the tool calls that do make it out are missing their unique identifiers, so anything downstream that matches calls to their results breaks. Each emitted call needs a proper unique id with a consistent format.

There's also a detection issue: the tool call block start marker can arrive from the tokenizer as a special token ID rather than as decoded text, and right now the parser only looks for the text form so it never enters tool-call mode in that case. It should catch the start whether it comes through as decoded text or as the special token ID.

And lastly the end-of-stream signal fires at the wrong time, it triggers on the tool call close marker when it should only fire on a genuine end-of-sequence token. Can you fix all of these in the streaming parser (content before calls, all parallel calls, unique ids, start detection by token id, and eos only on a real end-of-sequence token)?
