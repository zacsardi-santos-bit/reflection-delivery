I'm working on the ingress controller's route translation layer and I've found a bug with HTTPS redirect rules.

*   The envoyHTTPRoutes function must be modified so that when a route has a redirect action targeting a specific scheme (e.g., https), a header matcher for 'X-Forwarded-Proto' is added to that route's match conditions

*   The X-Forwarded-Proto header matcher added to redirect routes must have InvertMatch set to true and an exact string match equal to the redirect's target scheme, so the redirect only fires when the incoming request's X-Forwarded-Proto header does NOT already equal the target scheme

*   The header matcher's Name field must be exactly 'X-Forwarded-Proto'

*   Backend routes (routes with Backends but no redirect) must not receive any X-Forwarded-Proto header matchers; their Match.Headers must remain empty

*   The function must return routes in the same order as the input: redirect routes before backend routes when provided in that order

*   For a redirect route with scheme 'https', port 443, and status code 302, the resulting Envoy route must have a redirect action with scheme 'https', port redirect 443, and response code RedirectAction_FOUND

*   The cluster name for backend routes must use the format 'namespace:name:port' (e.g., 'default:backend:31337')


*   Interface details: Type: Function
Name: envoyHTTPRoutes
Location: operator/pkg/model/translation/envoy_virtual_host.go
Signature: envoyHTTPRoutes(httpRoutes []model.HTTPRoute, hostnames []string, hostNameSuffixMatch bool, listenerPort uint32) []*envoy_config_route_v3.Route
Description: Converts a list of model.HTTPRoute objects into Envoy route configurations for a virtual host. This existing function must be modified so that redirect routes whose RequestRedirect filter includes a scheme value have an X-Forwarded-Proto header matcher automatically appended to their route match conditions. The header matcher must have Name="X-Forwarded-Proto", InvertMatch=true, and an exact string match equal to the redirect's scheme. Backend routes (routes with no redirect) must not receive any such header matcher. The function signature and overall structure remain the same; only the redirect-handling logic is extended.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.