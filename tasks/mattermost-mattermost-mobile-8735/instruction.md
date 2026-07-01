Implement a configurable channel banner feature in the mobile app that allows admins to display custom text and background color for channels. Ensure the banner is visible only under specific conditions and is tappable to reveal more details. Additionally, create a utility function to determine appropriate text color based on banner background color.

*   Implement the `shouldShowChannelBanner` function in `app/screens/channel/channel_feature_checks.ts`:
    *   Return `false` if the `license` argument is not provided or falsy.
    *   Return `false` if the `bannerInfo` argument is not provided or falsy.
    *   Return `false` if the `license` SkuShortName is not 'premium'.
    *   Return `false` if `bannerInfo.enabled` is `false`.
    *   Return `false` if `bannerInfo.text` or `bannerInfo.background_color` is empty or falsy.
    *   Return `false` for DM and GM channel types; return `true` for open and private channels when all conditions are met.

*   Implement the `ChannelBanner` component in `app/screens/channel/header/channel_banner/index.ts`:
    *   Accept a `channelId` string prop and subscribe to the database to read the channel's `bannerInfo`.
    *   Render the banner text when the channel exists with valid, enabled, complete `bannerInfo`.
    *   Do not render visible content if `channelId` is empty, the channel does not exist, or `bannerInfo` is missing or incomplete.
    *   On press, call the bottomSheet navigation function with `{ title: 'Channel Banner', closeButtonId: 'channel-banner-close' }`.

*   Implement the `getContrastingSimpleColor` function in `app/utils/general/index.ts`:
    *   Accept a hex color string with or without a '#' prefix.
    *   Return '#FFFFFF' for dark colors and '#000000' for light colors based on WCAG relative luminance.
    *   Return an empty string for invalid inputs, including empty strings, whitespace-only, or malformed hex codes.
    *   Ensure '#747474' and '#737373' return '#FFFFFF'.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.