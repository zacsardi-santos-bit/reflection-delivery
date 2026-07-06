I'm doing some cleanup on the testing infra for our Rust bundler and want to reorganize how test config files get loaded. Right now the logic that reads a config file off disk lives as an associated function directly on the config data type, and that's annoying because if I ever want to add schema validation or nicer error reporting I'd have to bloat the type itself. It also scatters responsibilities across crates in a way that's a pain to maintain.

What I want is to pull the config-loading responsibility out into its own dedicated submodule inside the testing library. That submodule should re-export the config type so callers can still get at it, and also provide a standalone free function that takes a file path and returns a parsed config object. Behavior of the new function needs to match the old method exactly for all the existing test configurations, so nothing should break, it's just a move plus a cleaner shape.

Oh and the rest of the test infrastructure calls this thing in a ton of tests, so update all those call sites to use the new module path and function instead of the old method on the type.

The point is to keep the config data type focused on structure and deserialization while letting the loading routine grow on its own later (validation against a schema, that kind of thing) without touching the data type.
