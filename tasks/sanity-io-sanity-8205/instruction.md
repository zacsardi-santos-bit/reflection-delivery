Implement the `ReleaseDashboardFooter` component to correctly handle all release states by rendering contextually appropriate action buttons. Ensure that archived releases do not display a primary action button, leaving only the overflow menu button visible.

*   Update the `ReleaseDashboardFooter` component located at `packages/sanity/src/core/releases/tool/detail/ReleaseDashboardFooter.tsx`.
    *   Render a container element for footer actions with the attribute `data-testid` set to `'release-dashboard-footer-actions'`.
    *   For a release in the 'archived' state:
        *   Do not render any primary action button.
        *   Ensure only the overflow menu button appears, resulting in exactly 1 child element inside the `'release-dashboard-footer-actions'` container.
    *   For a release that is active and of the ASAP type:
        *   Render an element with `data-testid='publish-all-button'`.
    *   For an active scheduled release that is not yet scheduled/scheduling:
        *   Render an element containing the text `'Schedule for publishing...'`.
    *   For a release that has been published:
        *   Render an element containing the text `'Revert release'`.
    *   For a release in the scheduled state (scheduled for publishing):
        *   Render an element containing the text `'Unschedule for publishing'`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.