I'm working with the Matrix SDK's room list service and timeline module and I need two things added.

First, I need the way timelines are accessed through the room list service to change. Right now, calling the method to get a room's timeline also silently initializes it, which means I have no way to configure the timeline before it's created. I'd like to split this into two steps: one method call to initialize the timeline using a builder (there should also be a method to get a sensible default builder), and a separate synchronous call to retrieve the already-initialized timeline. If the timeline hasn't been initialized yet, the retrieval should indicate that clearly rather than auto-initializing.

Second, I want a new filter type for timeline events that makes it easy to whitelist or blacklist specific categories of events without writing custom predicate logic. For example, I should be able to say "only show room name events" or "hide all message events" using a simple, declarative filter. This filter type should plug into the existing event filter setting for the timeline. It should also be possible to distinguish room topic state events as their own category within the existing state event content type.

Both of these should be usable together — I want to be able to initialize a timeline with a custom event-type filter and then retrieve it cleanly.
