Implement two methods in the router service to inspect route information for a given URL without navigating or changing the application state. The first method should synchronously return basic route information, and the second should asynchronously return the route information along with the model data.

*   Implement the `recognize(url: string)` method in `packages/@ember/routing/router_service.ts`:
    *   Accept a URL string and return a `RouteInfo` object if the URL matches a known route, or `null` if it does not.
    *   Ensure the `RouteInfo` object includes:
        *   `name`: full dotted route name.
        *   `localName`: leaf segment of the route name.
        *   `parent`: parent `RouteInfo` node or `null`.
        *   `child`: child `RouteInfo` node or `null`.
        *   `params`: object of dynamic segment key-value pairs.
        *   `queryParams`: object of query parameter key-value pairs.
        *   `paramNames`: ordered array of dynamic segment name strings.
    *   Ensure no route transition or re-render occurs; the application's state remains unchanged.
    *   Throw an assertion error with the message 'You must pass a url that begins with the application\'s rootURL "/app/"' if the URL does not start with the application's configured rootURL.
    *   Support non-default rootURL configurations.

*   Implement the `recognizeAndLoad(url: string)` method in `packages/@ember/routing/router_service.ts`:
    *   Accept a URL string and return a Promise that resolves with a `RouteInfoWithAttributes` object after loading the matched route's model.
    *   Ensure the `RouteInfoWithAttributes` object includes all fields of `RouteInfo`, plus:
        *   `attributes`: the loaded model data.
        *   The `parent` field is also a `RouteInfoWithAttributes`, including its own attributes, params, and paramNames.
    *   Ensure no route transition or re-render occurs; the application's state remains unchanged.
    *   Throw an assertion error with the message 'You must pass a url that begins with the application\'s rootURL "/app/"' if the URL does not start with the application's configured rootURL.
    *   Return a rejected Promise with the message 'URL <url> was not recognized' if the URL does not match any known route.
    *   Return a rejected Promise with the error object if a route's model loading fails.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.