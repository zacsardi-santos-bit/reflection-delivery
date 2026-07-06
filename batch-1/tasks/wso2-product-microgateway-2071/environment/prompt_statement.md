I'm working on an API gateway that forwards traffic to backend services, and I've noticed that when the backend is slow or unresponsive, the gateway never times out — it just holds the connection open indefinitely. There's no maximum request duration or idle connection limit enforced at the routing level.

I'd like the gateway to automatically apply default timeout values to all upstream routes it creates, so that requests to slow backends are eventually terminated and callers receive a proper timeout error. The default maximum request duration should be 60 seconds, and the default idle timeout should be 300 seconds. Ideally these should be configurable through the gateway's configuration file so operators can tune them.

Could you add support for these default upstream route timeouts? The values should be applied whenever the gateway generates routing configuration for an API.
