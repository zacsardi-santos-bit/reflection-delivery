I'm chasing a few related retry and cancellation bugs in the streaming UI and they all feed into each other. The main thing is when I cancel an in-progress request, the UI sometimes keeps showing a "retrying..." status because retry events that land after the cancellation are still getting applied to the state. I want retry status cleared immediately on cancel, and any late retry events that arrive afterward should just be silently ignored so nothing stale sticks around.

Related to that, the escape-to-cancel hint shows up even when we're idle. If a loading phrase happens to be set, the cancel hint appears alongside it even though nothing's actually in progress. That hint should only show while a request is actively being processed (the responding state), never when idle, even if some loading phrase is lingering.

Same deal with retry status loading phrases, they show in the idle state when retry status data is still present. Those phrases should only render while actively responding, so when idle we produce no retry phrase at all regardless of whether retry status data is hanging around.

And there's an underlying cause in the retry utility itself. If an abort signal fires right at the point where a retry notification would go out (either during error handling or during the content-based retry evaluation path), the retry callback still runs instead of the operation aborting cleanly. I want the retry util to check the abort signal before invoking any retry callback, and if it's already aborted at that point, reject with an abort error without calling the callback and without logging any warnings.

Fixing these keeps the UI honest about what's really happening so we don't get "retrying..." indicators or cancel hints when there's nothing being retried or cancelled.
