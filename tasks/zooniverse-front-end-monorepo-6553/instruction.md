Refactor the user stats and group management helper functions to support internationalization by replacing hardcoded English strings with translation key strings. Ensure that these functions accept a translation function to dynamically generate labels based on the user's locale.

*   Update the `getHeaderItems` function:
    *   Accept a `t` translation function in its parameter object.
    *   Use `t(key)` to generate label values for all header items.
    *   Ensure `PrimaryHeaderItem.props.label` and `secondaryHeaderItems.props.label` use specific translation keys based on membership status:
        *   Group member: Use `t('GroupStats.headerItems.all')` and `t('GroupStats.headerItems.leave')`.
        *   Group admin: Use `t('GroupStats.headerItems.all')`, `t('GroupStats.headerItems.copy')`, `t('GroupStats.headerItems.share')`, and `t('GroupStats.manage')`.
        *   Public group with null membership: Use `t('GroupStats.headerItems.share')`.

*   Update the `getDateRangeLabel` function:
    *   Return translation key strings for `countLabel` and `timeLabel`.
    *   Use specific keys for `countLabel` based on date range granularity:
        *   Day: 'BarChart.day'
        *   Week: 'BarChart.weekOf'
        *   Month: 'BarChart.monthOf'
        *   Year: 'BarChart.year'
    *   Use specific keys for `timeLabel` based on time range:
        *   Minute-based: 'BarChart.minAbbrev'
        *   Hour-based: 'BarChart.hourAbbrev'

*   Update the `getUserGroupStatus` function:
    *   Return translation key strings for status scenarios:
        *   'GroupContainer.loginToJoin' when a joinToken is present and no auth user.
        *   'GroupContainer.joining' when `createGroupMembershipLoading` is true.
        *   'GroupContainer.joinFail' when `createGroupMembershipError` is present.
        *   Return `error.message` directly when `groupError` is present.
        *   'GroupContainer.noAuth' when no group and no authUserId.
        *   'GroupContainer.notFound' when no group but an authUserId is present.

*   Update the `getDateRangeSelectOptions` function:
    *   Return option labels as translation key strings:
        *   'MAINCONTENT.DATERANGE.LASTSEVENDAYS'
        *   'MAINCONTENT.DATERANGE.LASTTHIRTYDAYS'
        *   'MAINCONTENT.DATERANGE.THISMONTH'
        *   'MAINCONTENT.DATERANGE.LASTTHREEMONTHS'
        *   'MAINCONTENT.DATERANGE.THISYEAR'
        *   'MAINCONTENT.DATERANGE.LASTTWELVEMONTHS'
        *   'MAINCONTENT.DATERANGE.ALLTIME'
        *   'MAINCONTENT.DATERANGE.CUSTOM'

*   Update the MainContent component:
    *   Render the classifications tab with the label 'Classifications' (normal capitalization).

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.