I keep hitting a crash when I run convolutions with a filter that has a zero-size kernel dimension. Instead of getting something readable, the bad filter goes straight through to the hardware backend, and on GPU that turns into an unhandled low-level crash or just undefined behavior, super hard to diagnose.

What I want is early argument validation in the convolution ops so a zero-size kernel spatial dimension gets caught before any hardware execution starts. This should cover both 1D and 2D convs, so for a 1D conv that's a zero kernel length, and for 2D it's a zero kernel height or width. When any of those spatial dims is zero, I'd expect a clear Python-level error raised immediately rather than the framework passing the invalid input down to the device.

Also it needs to hold for all the configs, not just the simple case: standard convolutions and depthwise convolutions, the different data layouts/formats, and at least the common floating-point precisions. Basically no matter which device or dtype I'm on, a zero-size kernel should give me an actionable message that identifies the invalid kernel dimension instead of an opaque system-level failure.

The point is just making the guard fire strictly greater than zero on kernel dims up front, so users who accidentally build a zero-size kernel get told what's wrong.
