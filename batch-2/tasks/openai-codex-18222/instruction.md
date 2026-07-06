I'm working on a terminal UI that has a plugins popup, and right now it shows all available plugins in a single flat list with no way to filter by category.

*   The ChatWidget struct must include a field named plugins_active_tab_id of type Option<String>, initialized to None when constructing a ChatWidget

*   The plugins popup must display a tabbed interface navigable with left/right arrow keys, with at minimum: an 'All Plugins' tab, an 'Installed' tab, and an 'OpenAI Curated' tab, plus one tab per additional marketplace source

*   The 'Installed' tab header must include the subtitle text 'Installed plugins.' and a count line formatted exactly as 'Showing N installed plugins.' (where N is the number of installed plugins)

*   The 'Installed' tab must display only plugins that have been installed; non-installed plugins must not appear on this tab

*   Navigating to a different tab must automatically clear any active search query

*   The 'OpenAI Curated' tab header must include the subtitle 'OpenAI Curated marketplace.' and must show only plugins from the official curated marketplace source

*   Plugin row descriptions on the 'OpenAI Curated' tab must omit marketplace name labels — text of the form 'ChatGPT Marketplace ·' must not appear in rows displayed on this tab

*   When multiple marketplaces share the same display name, their tabs must be labeled with disambiguating suffixes in the format 'Name (K/N)', where K is the 1-based occurrence index and N is the total number of marketplaces with that name

*   Each marketplace-specific tab must use a unique identifier derived from the marketplace's file system path (formatted as 'marketplace:{path}'), allowing tabs with duplicate display names to be individually addressed

*   When plugin data is refreshed, the currently active tab must be preserved by its string ID so the user remains on the same tab after the refresh


*   Interface details: Type: Struct Field
Name: plugins_active_tab_id
Location: codex-rs/tui/src/chatwidget.rs
Signature: plugins_active_tab_id: Option<String>
Description: Field on the ChatWidget struct that tracks the string ID of the currently active tab in the plugins popup. Must be initialized to None when constructing a ChatWidget instance (including in manual test construction).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.