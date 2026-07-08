I'm updating the end-to-end test infrastructure for istio/proxy and hitting a bunch of breakage after bumping the Envoy control plane library, so I could use a hand getting it all compiling and passing again.

First problem is the test driver won't build. The discovery server init now wants a context as its first argument, and the snapshot cache API for assigning cluster and listener resources switched from named struct fields to map-style indexing, so I need to set resources by key instead. Also the logger interface grew two extra methods that aren't implemented yet, so those need stubbing out to satisfy it.

Second, all the Envoy config templates in the tests still use the old untyped filter config format, but the current build only accepts the typed format with a fully-qualified protobuf type URL. That hits both the HTTP connection manager filter and the Wasm plugin filters, and the Wasm ones also have to be wrapped in a typed struct envelope. Oh and there's a deprecated Envoy command-line flag being passed at startup that's no longer valid, drop it.

Third, it's a maintenance mess: big blocks of node metadata JSON and stats config YAML are copy-pasted as inline string constants across multiple test files. I want those pulled into shared external template files under the testdata directory and loaded via a helper that reads a file and returns its contents as a string, so it all lives in one place instead of being duplicated everywhere.

Last thing, one metric label assertion is wrong. The canonical service name for the outbound proxy node is currently set to the short app name but it should be the full workload name, so fix that value.

Without these the suite can't even compile against the current deps, which means we can't validate proxy behavior at all, and centralizing the config makes future upkeep way less painful.
