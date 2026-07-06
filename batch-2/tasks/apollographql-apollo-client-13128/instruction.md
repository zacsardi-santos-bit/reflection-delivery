I'm building a server-rendered React application with Apollo Client and running into a hydration mismatch problem.

*   When a component uses the query hook with both the SSR opt-out option and the skip option set simultaneously, server-side rendering must produce output reflecting a non-loading, ready state: loading must be false and networkStatus must be 7.

*   After server-side rendering a component with both the SSR opt-out and skip options, the Apollo client cache must remain empty — no query data should be stored in the cache.

*   When the server-rendered HTML produced under these conditions is hydrated on the client, there must be zero recoverable hydration errors. React's hydration must not detect any mismatch between server and client output.

*   After hydration and client-side mounting, the rendered DOM must consistently show loading as false, networkStatus as 7, and the data as the undefined fallback value — with no render ever transitioning to a loading or error state.

*   Every render throughout the component's lifecycle (during SSR, during hydration, and after mounting) must maintain consistent values: loading false, networkStatus 7, and data undefined. No render should briefly show a different state.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.