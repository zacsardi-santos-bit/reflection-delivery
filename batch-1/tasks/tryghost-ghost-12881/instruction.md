Add a direct link between member subscription records and Stripe price entries by updating the database schema and models. Ensure that the new schema supports a product-and-price hierarchy without cascading deletions, and update the schema integrity hash accordingly.

*   Update the database schema:
    *   Add a new column `stripe_price_id` to `members_stripe_customers_subscriptions` table.
        *   Type: string, maxlength: 255, nullable: false, unique: false, index: true, defaultTo: ''.
    *   Ensure foreign keys do not cascade deletions:
        *   `stripe_products.product_id` to `products.id` should not use cascadeDelete.
        *   `stripe_prices.stripe_product_id` to `stripe_products.stripe_product_id` should not use cascadeDelete.

*   Modify models:
    *   Update `StripeCustomerSubscription` model:
        *   `add()` method must accept `stripe_price_id` alongside `plan_id`.
    *   Export `StripePrice` from `core/server/models/stripe-price.js`:
        *   `add()` method must accept fields: `stripe_price_id`, `stripe_product_id`, `amount`, `interval`, `currency`, `active`, `nickname`, `type`.
    *   Export `StripeProduct` from `core/server/models/stripe-product.js`:
        *   `add()` method must accept fields: `product_id`, `stripe_product_id`.
    *   Export `Product` from `core/server/models/product.js`:
        *   `add()` method must accept fields: `name`, `slug`.

*   Implement database migrations:
    *   Create `09-add-price-id-column-to-subscriptions-table.js` to add `stripe_price_id` column.
    *   Create `10-populate-stripe-price-id-in-subscriptions.js` to populate `stripe_price_id` with `plan_id` values.

*   Update test data and fixtures:
    *   Ensure `DataGenerator.forKnex` exposes `products`, `stripe_products`, and `stripe_prices` arrays.
    *   Each `stripe_customer_subscriptions` entry must include a `stripe_price_id` matching `stripe_prices`.
    *   Insert fixtures in the following order: `products`, `stripe_products`, `stripe_prices`, `stripe_customer_subscriptions`.

*   Update schema integrity:
    *   Set `currentSchemaHash` in `test/unit/data/schema/integrity_spec.js` to 'c31e5e88461bbc015a9e50561d07f6f7'.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.