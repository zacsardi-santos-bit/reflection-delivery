I'm working on the Node.js SDK for a Pulumi project and none of the automation tests will run anymore. When I try to execute the test suite, the TypeScript compilation fails immediately because one of the modules imported by the runtime code doesn't have type declarations that the current TypeScript runner can find. This is a fatal compilation error that prevents every test from running — not just the ones related to the problematic module.

The test runner currently uses a require-based TypeScript registration mechanism. I'd like to switch it to a more modern approach that uses a Node.js import hook instead, which would be more tolerant of modules that lack explicit type declarations.

Additionally, some of the automation tests need updating to use fully qualified stack names (with organization and project components) rather than bare stack names, so they work correctly in shared test environments.

Can you fix the TypeScript tooling setup so that the tests compile and run successfully? The test that verifies concurrent configuration updates across multiple stacks should pass once the compilation issue is resolved.
