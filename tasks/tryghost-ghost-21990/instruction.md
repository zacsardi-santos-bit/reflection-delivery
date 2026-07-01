Implement a CAPTCHA verification service for the Ghost platform to protect endpoints from bot traffic. Create a middleware function that integrates with an external bot-detection provider and can be easily added to any HTTP route. Ensure the service is configurable and can handle various scenarios gracefully.

Requirements:

*   Implement the `CaptchaService` class in `ghost/captcha-service/index.js`.
    *   Export `CaptchaService` as the default module export.
    *   Constructor signature: `constructor({ enabled: boolean, secretKey: string, scoreThreshold: number })`.
    *   Method signature: `getMiddleware() -> Function`.

*   Configuration:
    *   Accept a configuration object with fields: `enabled` (boolean), `secretKey` (string), and `scoreThreshold` (number).

*   Middleware behavior:
    *   When the service is enabled:
        *   `getMiddleware()` must return an Express-compatible middleware function with the signature `(req, res, next)`.
        *   The middleware function must have a `.length` property of 3.
        *   Read the CAPTCHA token from `req.body.token`.
        *   If `req.body.token` is absent or falsy, call `next()` with an error whose message is 'hCaptcha token missing'.
        *   Use the `hcaptcha` npm package to call `hcaptcha.verify` with `secretKey` and `token`.
        *   If verification succeeds and the score is less than `scoreThreshold`, call `next()` with no error.
        *   If verification succeeds and the score is greater than or equal to `scoreThreshold`, call `next()` with an error message 'The server has encountered an error.'
        *   If the CAPTCHA verification call rejects, call `next()` with an error message 'Failed to verify hCaptcha token'.
    *   When the service is disabled (`enabled: false`):
        *   `getMiddleware()` must return a no-op middleware that calls `next()` immediately with no arguments.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.