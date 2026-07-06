Implement a new feature in the `PostsStatsService` class to integrate page view data from an analytics source with existing email engagement metrics. Ensure the service can provide a comprehensive view of post performance, handling cases where the analytics source is unavailable or returns errors gracefully.

*   Update the `PostsStatsService` class:
    *   Accept an optional `tinybirdClient` in the constructor options alongside the existing `knex` database client.

*   Implement the `getTopPostsViews` method with the following specifications:
    *   Accept an `options` object with `date_from` (string), `date_to` (string), `timezone` (string), and an optional `limit` (number).
    *   Immediately return an empty array if no analytics client is configured.
    *   Call the analytics client's `fetch` method with the endpoint `'api_top_pages'` and pass options transformed to camelCase: `{dateFrom, dateTo, timezone, limit}`.
        *   Map `date_from` to `dateFrom` and `date_to` to `dateTo`.
    *   Return an empty array if the analytics client returns an empty array for `'api_top_pages'`.
    *   Return an empty array if the analytics client throws an error.
    *   Return an empty array if no matching posts are found in the database using the `uuid` column.
    *   When matching posts are found, return an array of objects with fields:
        *   `post_id` (string): the post's database ID.
        *   `title` (string).
        *   `published_at` (number): Unix timestamp in milliseconds.
        *   `views` (number): visits value from the analytics response.
        *   `open_rate` (number): calculated as `opened_count / email_count * 100`.
        *   `members` (number): total `email_count` for the associated email record.

*   Modify the Tinybird client's `buildRequest` method:
    *   Remove `timezone` and `member_status` from the default URL parameters in the constructed request URL.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.