## Description

The Media Library page and its associated components don't behave well on narrow viewports or touch-only devices. Several layout and interaction problems exist:

- The page header doesn't stack vertically on very narrow screens — elements overflow instead of wrapping or reordering.
- When bulk selection or download controls are visible, they also overflow rather than wrapping to a new line.
- Error messages in the page use a fixed side-by-side layout that breaks on small screens.
- Filter labels are fully hidden on small screens, making them inaccessible to screen readers.
- The selected filter control doesn't expose its selected state through a proper accessibility attribute, so assistive technology may not announce it correctly.
- The audio preview button intercepts pointer events even when it is invisible, blocking interaction with elements behind it. On touch devices, the button is always invisible, making audio previews impossible to trigger.
- Clicking anywhere on a media card's preview image should navigate to the item detail, but currently only a small invisible overlay handles clicks.
- Clicking the video play button on a media card incorrectly triggers the card's navigation action as well.
- The download button on a card is too small to tap reliably on touch screens.
- The media detail modal's inner layout cannot shrink below its content size, causing overflow on narrow screens.
- The modal's action buttons (close, download, copy permalink) are too small for reliable touch interaction.
- There is no separate action area within the modal that is shown only on mobile, so users on narrow viewports lack access to download and permalink copy actions without scrolling.

## Expected Behavior

- The page header should stack vertically on very narrow viewports and switch to a side-by-side layout at a small viewport width threshold.
- Selection controls and header action toolbars should wrap to additional lines when horizontal space is limited.
- Error message containers should use a responsive stacked-then-side-by-side layout.
- Filter label text should be hidden visually on small screens but always readable by assistive technology.
- The selected filter control should expose its selected state via the appropriate accessibility attribute.
- The audio preview button should be invisible and non-interactive while hidden, and should reliably appear on hover. On touch-only devices, it should always be visible and interactive.
- Clicking the media card's preview image area should trigger the card action.
- Clicking video playback controls should not also trigger the card navigation action.
- The download button on cards must meet minimum touch target size requirements on touch devices.
- The modal layout should be able to shrink below its content size on narrow screens.
- All modal action buttons must meet minimum touch target sizes on touch devices.
- The modal must provide a dedicated mobile action area with download and permalink copy controls, separate from the desktop footer actions.

## Why This Matters

Users accessing the Media Library on mobile devices or narrow browser windows currently experience broken layouts and inaccessible controls. Fixing these issues ensures the interface is usable and accessible across all screen sizes and input methods.
