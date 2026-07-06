I'm working on a red team security testing setup wizard and I need help making it responsive and accessible.

*   The EstimationsDisplay component must accept a config prop of type Config (imported from the setup page types) and render estimate cards. The outermost container wrapping the 'Estimated Duration:' label must carry the responsive stacking classes 'flex-col' and 'sm:flex-row'.

*   The DefaultTestVariables component's top-level header row (containing 'Test Variables' and the 'Add Variable' button) must have the classes 'flex-col' and 'sm:flex-row'. The 'Add Variable' button itself must have the class 'self-start'. Each variable input row must also have 'flex-col' and 'sm:flex-row'.

*   The PageWrapper component's description element must carry the classes 'overflow-hidden', 'max-h-[320px]', and 'md:max-h-[100px]'. The footer navigation element (data-testid='page-navigation') must have 'left-0', 'right-0', and 'md:left-[var(--sidebar-width)]', and must set the CSS custom property '--sidebar-width' to '240px' via an inline style.

*   The PluginConfigDialog component's dialog root must have the classes 'flex', 'max-h-[85vh]', 'flex-col', and 'overflow-hidden'. The scrollable body area must have 'min-h-0', 'flex-1', and 'overflow-y-auto'. The footer containing action buttons must have 'shrink-0'. Array field items must render remove buttons with accessible names following the pattern 'Remove [Field Label] [1-based index]' (e.g., 'Remove Target System 1').

*   The Plugins component's tablist must have the classes 'w-full', 'flex-wrap', 'sm:flex-nowrap', 'max-w-full', 'justify-start', and 'overflow-visible'. The custom intents section must include a button with the accessible name 'Intent file format help'.

*   The PluginsTab component must render a top-level container (data-testid='plugins-tab-container') with classes 'flex-col' and 'lg:flex-row', and a selected-plugins sidebar (data-testid='selected-plugins-sidebar') with classes 'w-full', 'lg:sticky', and 'lg:w-80'. Configure buttons must be labeled 'Configure [Plugin Display Name]', documentation buttons must be labeled 'View documentation for [Plugin Display Name]', and remove buttons must be labeled 'Remove [Plugin Display Name]'. The display name for a plugin comes from the existing displayNameOverrides or categoryAliases lookup tables (e.g., 'indirect-prompt-injection' resolves to 'Indirect Prompt Injection', 'aegis' to 'Aegis Dataset').

*   The Purpose component's accordion header group for application detail sections must have 'flex-col', 'items-start', 'sm:flex-row', and 'sm:items-center'. The heading text itself must have the class 'text-left'.

*   The Review component must render remove buttons with accessible names: 'Remove plugin [plugin-display-label]' for plugins (where the label is the plugin's summary display name, e.g., 'Remove plugin sql-injection'), 'Remove strategy [Strategy Display Name]' for strategies (e.g., 'Remove strategy Basic'), 'Remove policy [Policy Display Name]' for custom policies where the display name is either the policy object's name field (for named policies) or 'Custom Policy [1-based index]' for string/unnamed policies (e.g., 'Remove policy Custom Policy 1'), and 'Remove intent [intent text]' for intents (e.g., 'Remove intent Cancel my order').

*   The AgentFrameworkConfiguration component's template file dialog must have the classes 'flex', 'max-h-[85vh]', 'flex-col', and 'overflow-hidden'. The scrollable body area must have 'min-h-0', 'flex-1', and 'overflow-y-auto'. The footer must have 'shrink-0'.

*   The CustomPoliciesSection component must render edit buttons labeled 'Edit policy [1-based index]' for each policy row (e.g., 'Edit policy 1', 'Edit policy 2').

*   The DigitalSignatureAuthTab component's PEM key input method card grid must have the classes 'grid-cols-1' and 'sm:grid-cols-3'.

*   The HttpAdvancedConfiguration component's tablist must have the classes 'grid-cols-2', 'md:grid-cols-3', 'xl:inline-flex', and 'xl:!h-10'.

*   The HttpEndpointConfiguration component must: (1) render the raw-request toggle row with 'flex-col' and 'sm:flex-row'; (2) render the method+URL controls row with 'flex-col' and 'sm:flex-row', the HTTP method combobox with 'w-full', 'sm:w-24', 'sm:shrink-0', and the URL input with 'min-w-0', 'flex-1'; (3) render the auto-fill dialog with 'flex', 'max-h-[90vh]', 'flex-col', 'overflow-hidden' on the dialog, 'min-h-0', 'flex-1', 'overflow-y-auto' on the body, and 'shrink-0' on the footer; (4) render header rows with 'flex-col', 'items-stretch', 'sm:flex-row', 'sm:items-center'; (5) label header remove buttons 'Remove header [1-based index]' (e.g., 'Remove header 1').

*   The ProviderTypeSelector component must render the search filter row with the parent container having 'flex-col' and 'sm:flex-row'. The search input wrapper must have 'w-full' and 'sm:w-64'.

*   The TargetTypeSelection component must render the quick-start prompt row with the classes 'flex-col' and 'sm:flex-row'.

*   The SessionsTab component must render header input rows with 'flex-col', 'items-stretch', 'sm:flex-row', 'sm:items-center'. Remove header buttons must have accessible names matching the pattern 'remove header' (case-insensitive), e.g., 'Remove header 1'.

*   The TestCaseGenerateButton component must use its tooltipTitle prop as the button's accessible name. When tooltipTitle is provided, the button must be queryable by that exact string as its accessible name. The TestCaseDialog plugin dropdown (data-testid='plugin-dropdown') must have the classes 'w-full' and 'sm:w-[280px]'.

*   The VerticalSuiteCard component must render configure buttons labeled 'Configure [Plugin Display Name]' and documentation buttons labeled 'View documentation for [Plugin Display Name]' for selected plugins. The display name is resolved via existing displayNameOverrides lookup (e.g., 'bola' resolves to 'Object-Level Authorization Bypass').

*   The AgenticStrategiesGroup component's header section containing the 'Agentic Strategies' title must have the classes 'flex-col', 'items-start', 'sm:flex-row', 'sm:items-center'.

*   The StrategySection component's header section containing the section title must have the classes 'flex-col', 'items-start', 'sm:flex-row', 'sm:items-center'.

*   The RedTeamSetupPage must render a sidebar with data-testid='redteam-setup-sidebar' that has the classes 'hidden' and 'md:flex'. It must also render a mobile actions area with data-testid='redteam-setup-mobile-actions' that has the class 'md:hidden'. The mobile actions area must include a 'Config' button that opens a dropdown menu with at minimum a 'Save Config' item, which triggers a 'Save Configuration' dialog.

*   The CustomIntentPluginSection component must render remove-intent buttons labeled 'Remove intent [1-based index]' (e.g., 'Remove intent 1'). When only one intent is present, its remove button must be disabled.


*   Interface details: Type: Component
Name: EstimationsDisplay
Location: src/app/src/pages/redteam/setup/components/EstimationsDisplay.tsx
Signature: EstimationsDisplay({ config }: { config: Config }) => JSX.Element
Description: New component that renders estimate cards (including "Estimated Duration:"). Accepts a config prop of type Config imported from ../types. The outermost wrapper of the estimate card row must carry the CSS classes "flex-col" and "sm:flex-row".

Type: Component
Name: DefaultTestVariables
Location: src/app/src/pages/redteam/setup/components/DefaultTestVariables.tsx
Description: Existing component updated for responsive layout. The section header row containing "Test Variables" and the "Add Variable" button must have classes "flex-col" and "sm:flex-row". The "Add Variable" button must have class "self-start". Each variable input row must have "flex-col" and "sm:flex-row".

Type: Component
Name: PageWrapper
Location: src/app/src/pages/redteam/setup/components/PageWrapper.tsx
Description: Existing component updated for responsive layout. The description element must carry classes "overflow-hidden", "max-h-[320px]", "md:max-h-[100px]". The footer navigation element must have data-testid="page-navigation", classes "left-0", "right-0", "md:left-[var(--sidebar-width)]", and an inline style setting "--sidebar-width: 240px".

Type: Component
Name: PluginConfigDialog
Location: src/app/src/pages/redteam/setup/components/PluginConfigDialog.tsx
Description: Existing component updated for scrollable dialog layout and array item accessibility. The dialog root must have classes "flex", "max-h-[85vh]", "flex-col", "overflow-hidden". The scrollable body must have "min-h-0", "flex-1", "overflow-y-auto". The footer must have "shrink-0". Array field item remove buttons must have accessible names "Remove [Field Label] [1-based index]".

Type: Component
Name: Plugins
Location: src/app/src/pages/redteam/setup/components/Plugins.tsx
Description: Existing component updated for responsive tab strip. The tablist must have classes "w-full", "flex-wrap", "sm:flex-nowrap", "max-w-full", "justify-start", "overflow-visible". The custom intents section must include a button with accessible name "Intent file format help".

Type: Component
Name: PluginsTab
Location: src/app/src/pages/redteam/setup/components/PluginsTab.tsx
Description: Existing component updated for responsive layout and accessible plugin action buttons. Must render a top-level container with data-testid="plugins-tab-container" and classes "flex-col" and "lg:flex-row". Must render the selected-plugins sidebar with data-testid="selected-plugins-sidebar" and classes "w-full", "lg:sticky", "lg:w-80". Configure buttons must have accessible name "Configure [Plugin Display Name]". Documentation buttons must have accessible name "View documentation for [Plugin Display Name]". Remove buttons must have accessible name "Remove [Plugin Display Name]".

Type: Component
Name: Purpose
Location: src/app/src/pages/redteam/setup/components/Purpose.tsx
Description: Existing component updated for responsive accordion headers. The header group for accordion sections must have classes "flex-col", "items-start", "sm:flex-row", "sm:items-center". The heading text element must have class "text-left".

Type: Component
Name: Review
Location: src/app/src/pages/redteam/setup/components/Review.tsx
Description: Existing component updated to label removal buttons. Plugin remove buttons must have accessible name "Remove plugin [plugin-id]". Strategy remove buttons must have accessible name "Remove strategy [Strategy Display Name]". Custom policy remove buttons must have accessible name "Remove policy [Policy Name] [1-based index]". Intent remove buttons must have accessible name "Remove intent [intent text]".

Type: Component
Name: AgentFrameworkConfiguration
Location: src/app/src/pages/redteam/setup/components/Targets/AgentFrameworkConfiguration.tsx
Description: Existing component updated for scrollable dialog layout. The dialog root must have classes "flex", "max-h-[85vh]", "flex-col", "overflow-hidden". The scrollable body must have "min-h-0", "flex-1", "overflow-y-auto". The footer must have "shrink-0".

Type: Component
Name: CustomPoliciesSection
Location: src/app/src/pages/redteam/setup/components/Targets/CustomPoliciesSection.tsx
Description: Existing component updated to label edit buttons. Edit buttons per policy row must have accessible name "Edit policy [1-based index]".

Type: Component
Name: DigitalSignatureAuthTab
Location: src/app/src/pages/redteam/setup/components/Targets/DigitalSignatureAuthTab.tsx
Description: Existing component updated for responsive PEM key input grid. The card grid must have classes "grid-cols-1" and "sm:grid-cols-3".

Type: Component
Name: HttpAdvancedConfiguration
Location: src/app/src/pages/redteam/setup/components/Targets/HttpAdvancedConfiguration.tsx
Description: Existing component updated for adaptive tablist. The tablist must have classes "grid-cols-2", "md:grid-cols-3", "xl:inline-flex", "xl:!h-10".

Type: Component
Name: HttpEndpointConfiguration
Location: src/app/src/pages/redteam/setup/components/Targets/HttpEndpointConfiguration.tsx
Description: Existing component updated for responsive layout, scrollable dialog, and accessible header remove buttons. The raw-request toggle row must have "flex-col" and "sm:flex-row". The method+URL row must have "flex-col" and "sm:flex-row". The HTTP method combobox must have "w-full", "sm:w-24", "sm:shrink-0". The URL input must have "min-w-0", "flex-1". The auto-fill dialog must have "flex", "max-h-[90vh]", "flex-col", "overflow-hidden" on the dialog root; "min-h-0", "flex-1", "overflow-y-auto" on the body; "shrink-0" on the footer. Header rows must have "flex-col", "items-stretch", "sm:flex-row", "sm:items-center". Header remove buttons must have accessible name "Remove header [1-based index]".

Type: Component
Name: ProviderTypeSelector
Location: src/app/src/pages/redteam/setup/components/Targets/ProviderTypeSelector.tsx
Description: Existing component updated for responsive search/filter layout. The filter row parent must have "flex-col" and "sm:flex-row". The search input wrapper must have "w-full" and "sm:w-64".

Type: Component
Name: TargetTypeSelection
Location: src/app/src/pages/redteam/setup/components/Targets/TargetTypeSelection.tsx
Description: Existing component updated for responsive quick-start row. The quick-start prompt row must have "flex-col" and "sm:flex-row".

Type: Component
Name: SessionsTab
Location: src/app/src/pages/redteam/setup/components/Targets/tabs/SessionsTab.tsx
Description: Existing component updated for responsive header rows and accessible remove buttons. Header input rows must have "flex-col", "items-stretch", "sm:flex-row", "sm:items-center". Remove header buttons must have accessible names matching /remove header/i.

Type: Component
Name: TestCaseGenerateButton
Location: src/app/src/pages/redteam/setup/components/TestCaseDialog.tsx
Description: Existing component updated to expose tooltipTitle as the button's accessible name. When the tooltipTitle prop is provided, the rendered button must be queryable by that string as its accessible name.

Type: Component
Name: TestCaseDialog
Location: src/app/src/pages/redteam/setup/components/TestCaseDialog.tsx
Description: Existing component updated for responsive plugin dropdown. The plugin dropdown element (data-testid="plugin-dropdown") must have classes "w-full" and "sm:w-[280px]".

Type: Component
Name: VerticalSuiteCard
Location: src/app/src/pages/redteam/setup/components/VerticalSuiteCard.tsx
Description: Existing component updated to label plugin action buttons. Configure buttons for selected plugins must have accessible name "Configure [Plugin Display Name]". Documentation buttons must have accessible name "View documentation for [Plugin Display Name]".

Type: Component
Name: AgenticStrategiesGroup
Location: src/app/src/pages/redteam/setup/components/strategies/AgenticStrategiesGroup.tsx
Description: Existing component updated for responsive header. The header section containing "Agentic Strategies" must have classes "flex-col", "items-start", "sm:flex-row", "sm:items-center".

Type: Component
Name: StrategySection
Location: src/app/src/pages/redteam/setup/components/strategies/StrategySection.tsx
Description: Existing component updated for responsive header. The header section containing the section title must have classes "flex-col", "items-start", "sm:flex-row", "sm:items-center".

Type: Component
Name: RedTeamSetupPage
Location: src/app/src/pages/redteam/setup/page.tsx
Description: Existing component updated for mobile layout. Must render a sidebar with data-testid="redteam-setup-sidebar" and classes "hidden" and "md:flex". Must render a mobile actions area with data-testid="redteam-setup-mobile-actions" and class "md:hidden". The mobile actions area must include a "Config" button that opens a menu with at minimum a "Save Config" item, which triggers a dialog titled "Save Configuration".

Type: Component
Name: CustomIntentPluginSection
Location: src/app/src/pages/redteam/setup/components/CustomIntentPluginSection.tsx
Description: Existing component updated to label and disable remove-intent buttons. Remove buttons must have accessible name "Remove intent [1-based index]". When only one intent exists, that remove button must be disabled.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.