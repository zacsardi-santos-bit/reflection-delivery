I'm working on extending Haystack with a generator component that can call HuggingFace-hosted text generation models. I need the component to support two modes: one that targets a model by name on HuggingFace's serverless inference API, and another that points to a self-hosted text generation inference server by URL.

The component should validate its configuration at startup — raising an error if no model name is given for the serverless mode, if the model doesn't actually exist, or if the URL for the self-hosted mode is missing or isn't a valid HTTP/HTTPS address. It should default to generating at most 512 new tokens if not told otherwise, and accept a list of stop words that get forwarded to the underlying API.

When running, it should accept a prompt string and optional per-call generation overrides, returning both the generated text replies and associated metadata. It also needs to support streaming: if a callback is provided, the component should call it once for each text chunk as it streams in.

The component should be fully serializable so it can be saved and reloaded as part of a Haystack pipeline. It should also be exported from the main generators module so it's discoverable alongside other generator components.

Alongside this, I also need a simple utility function that checks whether a string is a valid HTTP or HTTPS URL — it should return true only if the scheme is http or https and there's a non-empty host, and return false for anything else including other schemes, bare hostnames, incomplete URLs, and empty strings.
