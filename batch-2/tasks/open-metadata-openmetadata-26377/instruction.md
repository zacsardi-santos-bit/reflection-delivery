I'm working on the glossary term and tag detail pages in our data catalog application.

*   A new class GlossaryTermClassBase must be created at openmetadata-ui/src/main/resources/ui/src/utils/Glossary/GlossaryTermClassBase.ts and exported both as a named export and as a default singleton instance (named glossaryTermClassBase). A named type export GlossaryTermDetailPageTabProps must also be exported from the same file.

*   GlossaryTermClassBase.getGlossaryTermDetailPageTabs(props) must delegate entirely to the getGlossaryTermDetailPageTabs utility function from GlossaryTermUtils, passing the received props object directly, and return whatever that function returns.

*   GlossaryTermClassBase.getGlossaryTermDetailPageTabsIds() must return exactly 5 tab objects in this order: OVERVIEW, GLOSSARY_TERMS, ASSETS, ACTIVITY_FEED, CUSTOM_PROPERTIES. Each object must have an id field (the tab key), editable set to false, and layout set to an empty array.

*   A new named export getGlossaryTermDetailPageTabs(props: GlossaryTermDetailPageTabProps) must be created at openmetadata-ui/src/main/resources/ui/src/utils/Glossary/GlossaryTermUtils.tsx. When isVersionView is false, it must return exactly 5 tabs with keys in this order: OVERVIEW, GLOSSARY_TERMS, ASSETS, ACTIVITY_FEED, CUSTOM_PROPERTIES. When isVersionView is true, it must return exactly 1 tab with key OVERVIEW (GLOSSARY_TERMS must be absent).

*   In the ACTIVITY_FEED tab returned by getGlossaryTermDetailPageTabs, the tab label component's isActive prop must be true when activeTab equals EntityTabs.ACTIVITY_FEED, and false otherwise. Its count prop must equal feedCount.totalCount.

*   In the CUSTOM_PROPERTIES tab returned by getGlossaryTermDetailPageTabs, the tab children component's hasEditAccess prop must be true when the permissions object has EditAll or EditCustomFields set to true (and isVersionView is false). It must be false when both EditAll and EditCustomFields are false.

*   The TagClassBase class at openmetadata-ui/src/main/resources/ui/src/utils/TagClassBase.ts must have a new public method getAdditionalTagDetailPageTabs(tag: Tag, activeTab: string): TabProps[] that returns an empty array by default, regardless of the tag or activeTab arguments passed.

*   The TagPage component must call tagClassBase.getAdditionalTagDetailPageTabs with the fetched tag object (which includes a fullyQualifiedName field) and the current active tab string after tag data has been loaded, and spread the results into the tab items array.

*   The GlossaryTermsV1 component must call glossaryTermClassBase.getGlossaryTermDetailPageTabs to build its tab items. The props object passed to this call must NOT include an onExtensionUpdate property.

*   The GlossaryTermsV1 component must call getFeedCounts (from CommonUtils) on mount when isVersionView is false or undefined. It must NOT call getFeedCounts when isVersionView is true.


*   Interface details: Type: Class
Name: GlossaryTermClassBase
Location: openmetadata-ui/src/main/resources/ui/src/utils/Glossary/GlossaryTermClassBase.ts
Description: Base class for glossary term detail page configuration. Provides methods for generating and listing page tabs. Must be exported as a named export and also as a default singleton instance named `glossaryTermClassBase`.
Signature:
  getGlossaryTermDetailPageTabs(props: GlossaryTermDetailPageTabProps) -> TabProps[]
  getGlossaryTermDetailPageTabsIds() -> Tab[]

Type: Interface
Name: GlossaryTermDetailPageTabProps
Location: openmetadata-ui/src/main/resources/ui/src/utils/Glossary/GlossaryTermClassBase.ts
Description: Named export. Props accepted by getGlossaryTermDetailPageTabs. Must include the following fields:
  - glossaryTerm: GlossaryTerm
  - activeTab: EntityTabs
  - isVersionView: boolean
  - assetCount: number
  - feedCount: FeedCounts (with a totalCount field)
  - permissions: OperationPermission (with EditAll and EditCustomFields boolean fields)
  - assetPermissions: OperationPermission
  - viewCustomPropertiesPermission: boolean
  - assetTabRef: React.RefObject<AssetsTabRef>
  - tabLabelMap: Record<string, string>
  - handleAssetClick: function
  - handleAssetSave: function
  - getEntityFeedCount: function
  - setAssetModalVisible: function
  - setPreviewAsset: function

Type: Function
Name: getGlossaryTermDetailPageTabs
Location: openmetadata-ui/src/main/resources/ui/src/utils/Glossary/GlossaryTermUtils.tsx
Signature: getGlossaryTermDetailPageTabs(props: GlossaryTermDetailPageTabProps) -> TabProps[]
Description: Named export. Builds and returns the tab configuration array for the glossary term detail page. When isVersionView is false, returns 5 tabs in order: OVERVIEW, GLOSSARY_TERMS, ASSETS, ACTIVITY_FEED, CUSTOM_PROPERTIES. When isVersionView is true, returns only 1 tab: OVERVIEW. The ACTIVITY_FEED tab's label component must receive isActive (true when activeTab === EntityTabs.ACTIVITY_FEED, false otherwise) and count (feedCount.totalCount). The CUSTOM_PROPERTIES tab's children component must receive hasEditAccess = !isVersionView && (permissions.EditAll || permissions.EditCustomFields).

Type: Method (on existing TagClassBase class)
Name: getAdditionalTagDetailPageTabs
Location: openmetadata-ui/src/main/resources/ui/src/utils/TagClassBase.ts
Signature: getAdditionalTagDetailPageTabs(tag: Tag, activeTab: string) -> TabProps[]
Description: Public method on the TagClassBase class. Returns an empty array by default, regardless of the tag or activeTab values passed. Subclasses may override this to return additional tab configurations.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.