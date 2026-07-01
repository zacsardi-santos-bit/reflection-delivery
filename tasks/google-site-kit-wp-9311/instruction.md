Update the audience segmentation feature in the analytics module to track the user who completed the setup instead of using a boolean flag. Implement a notification component that informs users about the feature if they didn't set it up themselves, and ensure it can be dismissed permanently.

*   Replace the boolean `audienceSegmentationSetupComplete` field with `audienceSegmentationSetupCompletedBy`:
    *   Set default value to `null` in both PHP settings and JavaScript datastore.
    *   Store the user ID of the person who completed the setup, or `null` if not completed.
    *   Ensure the PHP field is sanitized to `null` if the value is not an integer.

*   Update the JavaScript datastore for MODULES_ANALYTICS_4:
    *   Implement `setAudienceSegmentationSetupCompletedBy(userIdOrNull)` to update the setting.
    *   Implement `getAudienceSegmentationSetupCompletedBy()` to retrieve the current setting value.

*   Modify the scroll utility function:
    *   Rename `getContextScrollTop` to `getNavigationalScrollTop(selector, breakpoint)` in `assets/js/util/scroll.js`.

*   Create the `AudienceSegmentationIntroductoryOverlayNotification` component:
    *   Location: `assets/js/modules/analytics-4/components/audience-segmentation/dashboard/AudienceSegmentationIntroductoryOverlayNotification.js`.
    *   Render an introductory message if `audienceSegmentationSetupCompletedBy` is an integer and the notification has not been dismissed.
    *   Render nothing if `AUDIENCE_SEGMENTATION_INTRODUCTORY_OVERLAY_NOTIFICATION` is in the dismissed items list.
    *   Include a "Show me" button that:
        *   Uses `getNavigationalScrollTop` to find the scroll position for `.googlesitekit-widget-area--mainDashboardTrafficAudienceSegmentation`.
        *   Scrolls to the target position with smooth behavior.
        *   Posts to the dismiss-item API to permanently dismiss the notification using `AUDIENCE_SEGMENTATION_INTRODUCTORY_OVERLAY_NOTIFICATION`.

*   Export `AUDIENCE_SEGMENTATION_INTRODUCTORY_OVERLAY_NOTIFICATION` as a named constant from the same file as the component.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.