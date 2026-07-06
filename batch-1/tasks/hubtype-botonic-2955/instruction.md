Implement support for WhatsApp-specific commerce message types by creating new components for single product messages, product lists, product carousels, and media carousels. Develop a utility function to convert camelCase keys to snake_case for API compatibility.

*   Implement the `WhatsappProduct` component:
    *   Render a `<message>` element with `type="whatsapp-product"`.
    *   Pass `body`, `catalogId`, `productId`, and `footer` as camelCase attributes.
    *   Export from `packages/botonic-react/src/components`.

*   Implement the `WhatsappProductList` component:
    *   Render a `<message>` element with `type="whatsapp-product-list"`.
    *   Pass `body`, `catalogId`, `header`, and `footer` as camelCase attributes.
    *   Serialize the `sections` prop to JSON with camelCase keys converted to snake_case.
    *   Export from `packages/botonic-react/src/components`.

*   Implement the `WhatsappProductCarousel` component:
    *   Render a `<message>` element with `type="whatsapp-product-carousel"`.
    *   Pass `templateName`, `templateLanguage`, and `bodyParameters` as camelCase attributes.
    *   Serialize the `cards` array to JSON with keys converted to snake_case.
    *   Auto-assign `cardIndex` based on its position in the array if not provided.
    *   Export from `packages/botonic-react/src/components`.

*   Implement the `WhatsappMediaCarousel` component:
    *   Render a `<message>` element with `type="whatsapp-media-carousel"`.
    *   Pass `templateName`, `templateLanguage`, and `bodyParameters` as camelCase attributes.
    *   Serialize the `cards` array to JSON with keys converted to snake_case.
    *   Auto-assign `cardIndex` and `buttonIndex` based on their positions if not provided.
    *   Export from `packages/botonic-react/src/components`.

*   Implement the `toSnakeCaseKeys` function:
    *   Accept a plain object and return a new object with keys converted from camelCase to snake_case.
    *   Handle nested objects, arrays, and numeric suffixes in key names.
    *   Return `undefined` if input is `undefined`.
    *   Export from `packages/botonic-react/src/util/functional`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.