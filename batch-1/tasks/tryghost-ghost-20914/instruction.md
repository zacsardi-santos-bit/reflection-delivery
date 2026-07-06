Refactor the email analytics service to separate event-fetching methods and implement a status inspection feature. Ensure the service can independently fetch "opened" events and other event types, and provide a method to inspect the running state of analytics jobs.

*   Implement the `EmailAnalyticsService` class in `ghost/email-analytics-service/lib/EmailAnalyticsService.js` with the following methods:
    *   `getStatus()`: Return an object with job status entries for 'latest', 'latestOpened', 'missing', and 'scheduled'. Each entry must include the job name and a 'running' boolean.
    *   `getLastNonOpenedEventTimestamp()`: Return the last non-opened event timestamp using `queries.getLastEventTimestamp()`, falling back to 30 minutes prior to the current time if null.
    *   `getLastOpenedEventTimestamp()`: Return the last opened event timestamp using `queries.getLastEventTimestamp()`, with the same fallback as above.
    *   `fetchLatestOpenedEvents()`: Fetch only "opened" events using the provider's `fetchLatest` method with `events: ['opened']`. Skip fetching if the end timestamp is before the begin timestamp.
    *   `fetchLatestNonOpenedEvents()`: Fetch non-opened events using the provider's `fetchLatest` method with `events: ['delivered', 'failed', 'unsubscribed', 'complained']`. Skip fetching if the end timestamp is before the begin timestamp.
    *   `schedule({begin, end})`: Register a time range for a scheduled fetch.
    *   `cancelScheduled()`: Cancel a pending scheduled fetch.
    *   `fetchScheduled({maxEvents})`: Return 0 if nothing is scheduled or if canceled. Return 0 if end is before begin. Call `processEventBatch` and `aggregateStats` when events are fetched.
    *   `fetchMissing()`: Retrieve missed events using `queries.getLastJobRunTimestamp` and fill in any missed analytics data.
    *   `processEventBatch(events, result, fetchData)`: Dispatch each event to the appropriate handler and update `fetchData.lastEventTimestamp`. Increment result's unprocessable count for null handler returns and unhandled for unknown event types.
    *   `aggregateEmailStats(emailId)`: Call `queries.aggregateEmailStats` with the provided email id.
    *   `aggregateMemberStats(memberId)`: Call `queries.aggregateMemberStats` with the provided member id.

*   Update the `queries` module in `ghost/core/core/server/services/email-analytics/lib/queries.js`:
    *   Rename `getLastSeenEventTimestamp` to `getLastEventTimestamp`.

*   Modify the `EmailAnalyticsProviderMailgun.fetchLatest` method in `ghost/email-analytics-provider-mailgun/lib/EmailAnalyticsProviderMailgun.js`:
    *   Accept an `events` option array and pass the first element as the 'event' parameter to the mail client.

*   Adjust the email fixture data to set `email_count: 0` for the relevant test email fixture.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.