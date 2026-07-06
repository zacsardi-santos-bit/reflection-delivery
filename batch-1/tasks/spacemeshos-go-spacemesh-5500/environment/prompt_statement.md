I'm working on the P2P networking layer and need to implement a more robust timeout mechanism for network connections. Right now we only have a single fixed timeout for requests, which is too simplistic. A connection that is actively transferring data should be allowed to continue past the basic timeout, but there must still be an absolute ceiling that no connection can exceed — even one that keeps sending small amounts of data.

I'd like to introduce a stream wrapper that manages deadlines dynamically: it adjusts the deadline forward as data flows (proportional to chunk transfer time), but enforces a hard maximum deadline that acts as an absolute cap. When a timeout does occur, the error should include how many bytes were transferred in each direction plus the values of both timeout parameters, so we can diagnose issues in production.

The peer discovery subsystem also needs new configuration knobs for controlling how often it retries advertising and finding peers. Being able to configure these delays is important for making tests reliable and for tuning production behavior.

Finally, the fetch layer's configuration struct needs to expose the hard timeout as a configurable field alongside the existing per-request timeout.
