I'm looking to add a utility to our codebase that lets me write generator-based logic once and run it in both synchronous and asynchronous contexts. The idea is: the generator yields at each point where an external operation is needed (like reading a file or fetching data), and the utility takes care of driving the generator — either synchronously with a sync callback, or asynchronously with an async callback — without any changes to the generator itself.

I need two driver functions: one that runs the generator synchronously and returns the final result directly, and one that runs it asynchronously and returns a promise of the final result. Both should forward any additional arguments to the generator. Both should also handle error cases: if the callback throws or rejects, that error should be thrown back into the generator so the generator's own try/catch logic can handle it.

Recursive generator delegation needs to work too, since I want to use this for walking dependency graphs where the traversal is naturally recursive.

The two functions should live in a new source module at packages/trampoline/src/trampoline.js and be exported as named exports.
