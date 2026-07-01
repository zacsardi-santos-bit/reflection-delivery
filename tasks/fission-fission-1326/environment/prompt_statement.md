I'm working with Fission's HTTP trigger ingress configuration and I need to add TLS support. Right now, when I create or update a route with ingress enabled, there's no way to specify a TLS secret — so all ingresses end up as plain HTTP, and I have to manually patch the Kubernetes ingress if I want HTTPS. 

I'd like to be able to pass a TLS secret name when creating or updating a route, and have the ingress automatically configured with TLS using that secret and the associated host. I also need the ability to remove TLS from an existing route by passing a special value. If no TLS option is provided during an update, the existing TLS configuration should be left as-is.

The ingress configuration logic and the Kubernetes ingress spec generation both need to be updated to support this. The data model that represents an ingress configuration also needs a new field to hold the TLS secret name.
