I'm working on the Media Library section of the app and I've noticed it has several responsive layout and touch-accessibility problems that need to be fixed.

*   The Media page header outer container (grandparent of the 'Media Library' heading) must have CSS classes 'flex-col' and 'min-[390px]:flex-row', so it stacks vertically on very narrow viewports and switches to a side-by-side layout at 390 px and above.

*   When selection mode is active, the container element that is the direct parent of the 'N of M selected' count text must have CSS class 'flex-wrap' so selection controls can wrap to a second line on narrow screens.

*   While a bulk download is in progress, the container that is two levels above the 'Cancel download' button must have CSS class 'flex-wrap' so header actions can wrap on narrow screens.

*   The parent element of a 'not found' error message text must have CSS classes 'flex-col' and 'sm:flex-row' so the error message layout is responsive.

*   The AudioPreviewButton component (exported as a named export from src/app/src/pages/media/components/AudioPreviewButton.tsx) must accept props 'audioUrl' (string) and 'hash' (string).

*   The AudioPreviewButton's play button (accessible name 'Play audio preview') must initially have CSS classes 'pointer-events-none' and 'opacity-0', making it visually hidden and non-interactive until the user hovers over its parent element, at which point it must transition to 'pointer-events-auto' and 'opacity-100'.

*   The AudioPreviewButton's play button must always have CSS classes '[@media(hover:none)]:pointer-events-auto' and '[@media(hover:none)]:opacity-100' so it remains fully visible and interactive on touch-only devices regardless of hover state.

*   In the MediaCard component, clicking the image/preview surface (role 'img') must invoke the card's onClick callback, making the entire preview area a clickable region.

*   In the MediaCard component for video items, clicking the 'Play video preview' button must NOT invoke the card's onClick callback — the video preview control must be independent from the card navigation action.

*   In the MediaCard component, the 'Download' button must have CSS classes '[@media(hover:none)]:h-11' and '[@media(hover:none)]:w-11' so it meets minimum touch target size on touch-only devices.

*   In the MediaFilters component, the text label span inside each type-filter control (e.g., the tab for 'Videos') must have CSS classes 'sr-only' and 'sm:not-sr-only', so labels are visually hidden on small screens but always available to assistive technology.

*   In the MediaFilters component, the currently selected type-filter control must have the attribute 'aria-selected' set to 'true'.

*   In the MediaModal component, the last child element of the dialog element must have CSS classes 'min-w-0' and 'w-full'. The element with data-testid 'media-modal-details-panel' must have CSS classes 'min-h-0' and 'overflow-hidden' so the panel can shrink on narrow screens without overflowing.

*   The MediaModal component must render an element with data-testid 'media-modal-desktop-actions'. This element must have CSS classes 'hidden', 'md:flex', and 'shrink-0', and must contain a button with accessible name 'Download' (class '[@media(hover:none)]:h-11') and a button with accessible name 'Copy permalink' (classes '[@media(hover:none)]:h-11' and '[@media(hover:none)]:w-11').

*   The MediaModal component must render an element with data-testid 'media-modal-mobile-actions'. This element must contain a button with accessible name 'Download' (class 'h-11') and a button with accessible name 'Copy permalink' (classes 'h-11' and 'w-11'). The 'Close' button in the modal must have CSS classes 'h-11' and 'w-11'.


*   Interface details: Type: Component
Name: AudioPreviewButton
Location: src/app/src/pages/media/components/AudioPreviewButton.tsx
Description: Named export. Renders a play/pause button overlay for audio preview. The button must have accessible name 'Play audio preview' (when not playing). It is initially invisible and non-interactive (CSS classes 'pointer-events-none' and 'opacity-0'). Hovering over its parent makes it visible and interactive ('pointer-events-auto' and 'opacity-100'). On touch-only devices it is always visible and interactive via CSS classes '[@media(hover:none)]:pointer-events-auto' and '[@media(hover:none)]:opacity-100'.
Signature: AudioPreviewButton({ audioUrl: string, hash: string }): JSX.Element

Type: Component (modified)
Name: MediaCard
Location: src/app/src/pages/media/components/MediaCard.tsx
Description: The preview image area (role 'img') must be clickable and invoke the card's onClick callback. For video items, the 'Play video preview' button must NOT invoke the card's onClick callback. The 'Download' button must carry CSS classes '[@media(hover:none)]:h-11' and '[@media(hover:none)]:w-11'.

Type: Component (modified)
Name: MediaFilters
Location: src/app/src/pages/media/components/MediaFilters.tsx
Description: Each type-filter tab control must contain a text label wrapped in a span with CSS classes 'sr-only' and 'sm:not-sr-only'. The currently selected filter tab must have attribute aria-selected="true".

Type: Component (modified)
Name: MediaModal
Location: src/app/src/pages/media/components/MediaModal.tsx
Description: The modal layout container (last child of the dialog element) must have CSS classes 'min-w-0' and 'w-full'. The details panel element must carry data-testid="media-modal-details-panel" and have CSS classes 'min-h-0' and 'overflow-hidden'. The 'Close' button must have CSS classes 'h-11' and 'w-11'. A mobile-only action area must have data-testid="media-modal-mobile-actions" and contain a 'Download' button (class 'h-11') and a 'Copy permalink' button (classes 'h-11' and 'w-11'). A desktop-only footer action area must have data-testid="media-modal-desktop-actions" and CSS classes 'hidden', 'md:flex', and 'shrink-0'; it must contain a 'Download' button (class '[@media(hover:none)]:h-11') and a 'Copy permalink' button (classes '[@media(hover:none)]:h-11' and '[@media(hover:none)]:w-11').

Type: Component (modified)
Name: Media (page)
Location: src/app/src/pages/media/Media.tsx
Description: The outer flex container for the page header (grandparent of the 'Media Library' heading) must have CSS classes 'flex-col' and 'min-[390px]:flex-row'. When selection mode is active, the container that is the direct parent of the selection-count text must have CSS class 'flex-wrap'. The container two levels above the 'Cancel download' button must have CSS class 'flex-wrap'. The parent element of any 'not found' error text must have CSS classes 'flex-col' and 'sm:flex-row'.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.