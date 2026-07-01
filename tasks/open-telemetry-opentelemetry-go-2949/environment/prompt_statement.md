I'm working on the OpenTelemetry Go metric SDK and I need to add temporality configuration support to the reader types. Right now, readers don't expose any way to specify whether they prefer cumulative or delta reporting, and there's no option to set this at construction time. Different metric backends need different temporality styles, so I need each reader to be configurable.

Specifically, I'd like to be able to pass an option when constructing a manual reader or periodic reader that specifies a function to select the temporality for each instrument kind. If no option is provided, the default should be cumulative temporality. If someone passes the same option more than once, the last one should win.

The reader interface itself should also reflect that temporality is part of a reader's contract — so that any type implementing the reader interface must declare how it handles temporality for a given instrument kind.
