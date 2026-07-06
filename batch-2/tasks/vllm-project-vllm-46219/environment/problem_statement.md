I'm hitting an annoying limitation in the completions API and I think it's just an arbitrary guard that needs to go. When I send a prompt as a list of token IDs and also set echo, the server flat-out rejects the request with a validation error complaining that echo isn't supported with token-ID prompts. That doesn't make sense to me. Clients that work at the token level should get echo the same as anyone passing a plain string.

What I want is for the server to decode those token IDs back into text and prepend that decoded text to the completion text in the response, exactly like it does when the prompt comes in as a string. Important detail though, the engine should still receive the original token IDs, not the decoded text. Only the echoed portion in the response shows the decoded result. And usage counts need to stay correct, so the prompt token count should reflect the number of input token IDs.

There's a related interaction with the return-token-IDs option that also has to behave. If I ask for both echo and token-ID output, the main text field should contain only the generated text (no decoded prefix jammed in there), and the prompt's token IDs and the completion's token IDs come back in their own separate dedicated fields instead of being mixed into the text output.

Oh and this needs to work in streaming too. When echo is on with a token-ID prompt in a streaming request, I'd expect a chunk carrying the decoded prompt text to show up before the first generated token chunk.

So basically: drop the rejection, decode for the echo prefix, keep original token IDs going to the engine, split prompt vs completion token IDs into their own fields when token-ID output is requested, and mirror all of that in streaming mode.
