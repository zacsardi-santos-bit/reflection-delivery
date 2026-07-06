Implement enhancements to the Gatsby Recipes system by updating the plugin and shadow file resource providers to ensure they function correctly across various project configurations. Ensure that the plugin resource can handle minimal configurations and that the shadow file resource fully supports file lifecycle operations.

*   Update the GatsbyPlugin resource:
    *   Ensure it supports projects with a minimal Gatsby configuration file where the plugins array is initially empty.
    *   Verify create, read, update, destroy, and plan operations work correctly with the gatsby-starter-hello-world fixture.
    *   Organize plugin resource test fixtures into subdirectories: 'fixtures/gatsby-starter-blog' and 'fixtures/gatsby-starter-hello-world'.

*   Enhance the ShadowFile resource:
    *   Implement full support for create, read, update, destroy, and plan operations.
    *   Ensure the create operation:
        *   Accepts a context object with a 'root' property and an object with 'theme' and 'path' properties.
        *   Reads source files from 'node_modules/<theme>/src/<relativePath>' and writes them to 'src/<theme>/<relativePath>', creating directories as needed.
        *   Returns a resource object with 'id', 'theme', 'path', 'contents', and '_message' fields.
    *   Ensure the read operation:
        *   Accepts the project root context and a resource 'id'.
        *   Returns the resource object or undefined if the file does not exist.
    *   Ensure the plan operation:
        *   Accepts a context object and an object with 'theme', 'path', and optional 'id' fields.
        *   Returns an object with 'id', 'theme', 'path', 'diff', 'currentState', 'newState', and 'describe' fields.
    *   Ensure the destroy operation:
        *   Accepts the project root context and an object with an 'id' field.
        *   Deletes the shadowed file and returns the resource object that was deleted.
    *   Expose 'schema', 'validate', 'create', 'update', 'read', 'destroy', and 'plan' exports in the shadow-file module.

*   Ensure the file resource's create operation:
    *   Calls fs.ensureFile before writing file contents to automatically create intermediate directories.

*   Use fixed fixtures directory for shadow file tests and clean up the 'src' subdirectory before and after each test run.

*   Include necessary fixture files:
    *   'gatsby-starter-blog/gatsby-config.js' for a blog-style starter.
    *   'gatsby-starter-hello-world/gatsby-config.js' for a minimal hello-world starter.
    *   'node_modules/gatsby-theme-blog/src/components/author.js' containing a React component.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.