I'm working on migrating our project's classification page from a legacy format to a standard JavaScript module. The current implementation can't be imported or tested with our standard test tooling because it's written in an older file format that doesn't support default exports the way our test suite expects.

I need the classification page to be rewritten as a proper JavaScript module with a default export, so that it can be imported and rendered in tests. Once it's a proper module, it should behave as expected: when rendered with a project (even without a logged-in user), it should display the main classification container. Additionally, the "project finished" banner should only appear when the project is actually complete — it should never be shown when the completion flag isn't set.

Can you help migrate the classification page component to a standard JavaScript module format, making sure it exports the component as the default export and preserves the correct rendering logic?
