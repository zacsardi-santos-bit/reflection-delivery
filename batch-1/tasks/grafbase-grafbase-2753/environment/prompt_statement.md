I'm working on the Grafbase CLI and noticed that when I use the extension initialization command to scaffold a new resolver or authentication extension project, the generated configuration file references an outdated version of the SDK library rather than the current release. The version string appears in both the regular dependencies and the dev-dependencies sections of the generated file, and both need to be updated.

There's also a related compilation issue in the codebase itself — the SDK library and the code that depends on it currently do not compile cleanly. The compilation fix and the version reference update need to go together so that both the generated templates and the build system are in a consistent, working state.

Could you update the embedded SDK version reference in the CLI's extension project template to match the current SDK release, and fix the underlying compilation issues so the whole project builds correctly?
