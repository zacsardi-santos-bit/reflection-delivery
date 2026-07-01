I'm working with the rosbag2 player and I need a way to stop playback cleanly without having to shut down all of ROS. Right now, if I want to terminate a bag replay that's running in a background thread, there's no clean way to do it — the only option is a full ROS shutdown, which kills everything else in my application too.

What I need is a stop capability that works in multiple situations: stopping while the player is paused, stopping during active message publishing, and calling stop before playback has even started (which should be a safe no-op). I also need the player to stop itself automatically when it goes out of scope — destroying the player should interrupt any ongoing playback and let the play thread return cleanly within a short time.

Additionally, I need a ROS service endpoint on the player node so that external nodes can request a stop over the network — similar to how the existing pause and resume services work. This service should be available and ready for client connections as part of the player's normal setup.

When stop is triggered during active playback, it should respect any in-flight message that is currently being dispatched — that message can finish — but no new messages should be published after the stop is requested.
