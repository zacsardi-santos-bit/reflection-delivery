I'm working on improving the main menu component's accessibility and test utilities.

*   The getMenuLabels utility function must accept no arguments (remove the RenderResult parameter); it must locate the menu container using the accessible role 'menu' with accessible name 'Main menu', then collect and return the text labels of all visible action menu items within it.

*   The openMenu utility function must locate the main menu button via the accessible role 'button' with accessible name 'Main menu' when simulating a click to open the menu.

*   The main menu button element must be queryable by accessible role 'button' with accessible name 'Main menu'; it must carry aria-label='Main menu', aria-haspopup='menu', and aria-expanded set to 'false' when closed and 'true' when open.

*   The menu container element must be queryable by accessible role 'menu' with accessible name 'Main menu'; it must carry role='menu' and aria-label='Main menu'.

*   Standard menu action items (Rerun, Clear cache, Print, Get help, Report a bug, About, View app source, Report bug with app) must each be queryable by role='menuitem' with their label text as the accessible name; disabled items must carry aria-disabled='true'.

*   Screen recording menu items must be queryable by role='menuitem' with accessible names 'Record screen', 'Cancel recording', or 'Stop recording' depending on the current recording state.

*   The auto-rerun toggle item must be queryable by role='menuitemcheckbox' with an accessible name containing 'Auto rerun'; it must carry aria-checked and aria-disabled attributes as appropriate.

*   Theme radio items must be queryable by role='menuitemradio' with their theme name (e.g., 'Light', 'Dark', 'System') as the accessible name; the theme group container must be queryable by role='group' with aria-label='Theme'.

*   Menu divider elements must be queryable by role='separator'.


*   Interface details: Type: Function
Name: getMenuLabels
Location: frontend/app/src/components/MainMenu/mainMenuTestHelpers.ts
Signature: getMenuLabels() -> string[]
Description: Returns the text labels of all visible action menu items. Must take no arguments. Must locate the menu container using screen.getByRole("menu", { name: "Main menu" }), then return all label strings found within it by querying data-testid="stMainMenuItemLabel" elements.

Type: Function
Name: openMenu
Location: frontend/app/src/components/MainMenu/mainMenuTestHelpers.ts
Signature: openMenu() -> Promise<void>
Description: Opens the main menu popover by clicking the menu trigger button. Must locate the button using screen.getByRole("button", { name: "Main menu" }) instead of a testid query.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.