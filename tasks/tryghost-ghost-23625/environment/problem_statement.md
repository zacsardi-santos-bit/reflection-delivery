## Description

The posts statistics service provides detailed data on subscriber conversions attributed to posts, but currently has no way to surface page view counts. This means dashboards can show which posts drove the most sign-ups or revenue, but not which posts received the most traffic. With an analytics data source already integrated elsewhere in the platform, we should be able to combine traffic data with email engagement metrics for a complete per-post performance picture.

## Expected Behavior

- A new endpoint on the posts statistics service should return top posts ranked by page view count
- Each result should include the post identifier, title, publish date, total views, email subscriber count, and email open rate
- When the analytics data source is unavailable or returns no data, the method should return an empty result set rather than throwing
- If the analytics source encounters an error, the method should handle it gracefully and return an empty array
- Date range and timezone parameters specified in the request should be forwarded correctly to the analytics data source

## Why This Matters

Content teams need a unified view of post performance that combines web traffic and email engagement data. Currently, they must look at traffic and subscriber metrics in separate places, making it difficult to identify which content performs best overall.

## Additional Notes

Some parameters that were previously sent by default with every analytics data request are no longer needed and should be removed from the default request construction, keeping queries clean and minimal.
