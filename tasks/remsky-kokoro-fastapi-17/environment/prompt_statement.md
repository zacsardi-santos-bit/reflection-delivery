I'm working on a text-to-speech FastAPI service and running into two related problems that I need to fix.

The first is that the phoneme conversion endpoint crashes when I send it an empty text string. Instead of returning a clean error response, the server throws an unhandled exception. I'd like it to validate the input and return a structured HTTP 500 error response with an "error" field in the response body when the text is empty.

The second, bigger issue is that the model layer has no way to retrieve the active model instance or query the current hardware device. The service class that wraps the model has to initialize things from scratch each time, and there's no stable hook for tests to substitute a lightweight stand-in for the real model. I need the model class to expose a method for getting the current instance (raising a clear error if it hasn't been initialized yet) and a method for getting the device, and the service class should call the instance accessor during its own initialization so the dependency is explicit. The model setup flow should also properly store the initialized model object on the class so the accessor can return it later.

These two changes together will let me properly test the service layer in isolation and give API callers meaningful error responses instead of server crashes.
