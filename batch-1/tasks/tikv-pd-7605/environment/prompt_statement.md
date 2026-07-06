I'm working on the pd-ctl tool and I need help setting up a proper, lifecycle-managed client for communicating with the PD server, as well as adding typed support for querying cluster information.

Right now, the cluster subcommands use raw HTTP calls that return untyped string responses. There's no structured client that is initialized with server addresses and shared across commands — so there's nothing to properly close when the tool exits. I need the PD HTTP client interface to include typed methods for fetching cluster metadata and cluster status, along with a corresponding data type for cluster state that captures initialization status, replication status, and bootstrap time.

I also need a globally accessible client variable in the command package, together with a setup function that initializes this client from a list of server addresses and properly closes any previously existing client. The cluster subcommands should be updated to use this typed client and output results as formatted JSON.

Finally, the test helper used to execute commands needs to initialize this client (using the server address passed as an argument) before executing any command, and clean it up via a deferred close. Without these changes, integration tests for cluster and ping operations fail.
