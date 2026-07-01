## Description

The bag playback system currently has no way to stop an ongoing playback session cleanly. The only way to terminate a running playback is to shut down the entire ROS node context, which is too destructive for real-world use cases where you want to stop the bag replay while keeping the rest of the system alive.

We need a way to stop playback that:
- Can be triggered programmatically from another thread
- Can be triggered remotely via a ROS service call
- Happens automatically and safely when the player object is destroyed

## Expected Behavior

- Calling stop on the player while it is paused should cause the play loop to exit immediately, without hanging and without requiring a full ROS shutdown
- Calling stop during active playback should let the currently in-flight message finish publishing, then exit the loop — no additional messages should be published after stop is requested
- Calling stop before playback has started should be a safe no-op (no hang, no error)
- Destroying the player while playback is running should implicitly stop it — the play loop should terminate within a short timeout
- A new ROS service endpoint should be available on the player node so that external nodes can request playback to stop over the network

## Why This Matters

Without a clean stop mechanism, users are forced to shut down all of ROS just to stop bag replay. This prevents composable and reusable use of the player in larger systems where the rest of the application should continue running after the bag is done or is no longer needed.
