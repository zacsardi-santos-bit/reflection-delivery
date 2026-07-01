I'm working with Bevy's ECS systems and I need to be able to retrieve a system's component access information directly from the initialization step. Right now, when I initialize a system, the call returns nothing — so if I want to inspect what components the system reads or writes, or check for access conflicts with another system, I have to reach for separate, indirect mechanisms.

It would be much more ergonomic if the initialization method returned the system's component access set directly. That way, right after initializing a system, I could immediately call conflict-detection methods on the result without any extra steps.

Can you update the system initialization method so it returns the component access set, and make sure all existing system types (including those built from ordinary functions via the standard conversion mechanism) implement this updated interface?
