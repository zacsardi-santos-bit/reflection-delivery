I'm working on the Gen 1 to Gen 2 Amplify migration code generator and running into a few issues I'd like to address.

First, the generated backend code currently hardcodes the Gen 1 environment name as a static value. I want it to read the environment name dynamically at runtime instead. When the generated code runs inside a CI environment and the environment name isn't set, it should throw a descriptive error right away. When running locally (outside CI), it should fall back to a default value of "sandbox". The generated storage bucket name should also use this dynamic environment name rather than a hardcoded string — the bucket identifier passed to the renderer includes the environment name as its last dash-separated segment, and I want that segment replaced with the dynamic value at runtime.

Second, the data source code generator currently writes a TODO placeholder comment instead of the actual schema. I want it to accept the real GraphQL schema as input and include it in the generated output as a template literal variable. The old placeholder export and TODO error should be removed.

Third, the migration tooling needs a way to actually fetch the GraphQL schema from the Gen 1 project files. It should look for the AppSync API resource in the project metadata, then check for a schema folder with multiple schema files (merging them all together), or fall back to a single schema file. If neither is found, it should throw a descriptive error indicating that no schema could be located in the project.

Finally, the generated project's package configuration should include the CI detection library as a dev dependency so the runtime CI checks can function correctly.
