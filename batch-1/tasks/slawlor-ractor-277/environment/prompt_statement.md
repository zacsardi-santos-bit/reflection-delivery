I'm building a supervision tree with ractor and running into a couple of gaps in the API. First, when a supervisor is shutting down, I want to cleanly stop or drain all of its linked children in the `post_stop` lifecycle hook and wait for them to fully finish — including making sure each child's own cleanup hook runs. Right now there's no built-in way to do this, so I'd have to manually track and iterate over children myself.

Second, I'd also like to be able to stop or drain all children from *outside* the supervisor (i.e., an external caller tells the supervisor to stop all its children). When that happens, the children shutting down should naturally trigger the parent supervisor's own shutdown through the supervision tree notifications.

Finally, once I convert a typed actor reference into a type-erased cell (losing the generic message type information), I'd like to be able to check at runtime whether that actor handles a particular message type. For local actors this should return a definitive yes or no; for remote actors it should indicate that this check isn't possible.

Could you add these capabilities to `ActorCell` (and make them accessible from typed actor references as well)?
