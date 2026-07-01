Implement support for reading a project configuration file in Cloudflare Pages local development. Ensure that the configuration file is automatically applied when running local development, with command line arguments taking precedence over configuration file values.

*   Update error messages:
    *   When no directory, proxy command, or proxy port is specified, ensure the error message is: "Must specify a directory of static assets to serve, or a command to run, or a proxy port, or configure `pages_build_output_dir` in `wrangler.toml`."
    *   When using a custom config file path via the --config flag, ensure the error message is: "Pages does not support custom paths for the `wrangler.toml` configuration file."
    *   For unsupported environment names in the configuration, ensure the error message:
        *   States: "contains the following environment names that are not supported by Pages projects:"
        *   Quotes each environment name individually with double-quotes.
        *   Uses proper 4-space indentation for continuation lines.
    *   For conflicting configuration keys in the config file, ensure the continuation line starting with 'Please use...' has proper 4-space indentation.

*   Implement configuration file support:
    *   Ensure the Pages local development command reads a `wrangler.toml` file at the project root and applies its top-level configuration, including vars, kv_namespaces, d1_databases, r2_buckets, services, durable_objects bindings, and ai bindings.
    *   When `pages dev` is invoked with a `<directory>` argument but `pages_build_output_dir` is not specified in `wrangler.toml`, ensure the configuration is still read and applied.
    *   Ensure command line flags override matching bindings from the config file, while non-overridden bindings from the config file are retained.

*   Ensure compatibility with different project types:
    *   For Pages Functions projects (using a `functions/` directory), ensure static pages are served and custom routing rules from a `_routes.json` file are applied when running with `wrangler pages dev`.
    *   For Pages Advanced Mode projects (using a `_worker.js` file), ensure static pages are served, the worker runs, and custom routing rules from a `_routes.json` file are applied when running with `wrangler pages dev`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.