Implement the ability to pass extra flags to specific macOS package management operations to resolve ambiguities between command-line formulae and desktop applications (casks). Update functions to handle both package types and include aliases in package listings.

*   Update `latest_version` function in `salt/modules/mac_brew_pkg.py`:
    *   Accept an optional `options` parameter (list of strings, default None).
    *   Insert `options` between '--json=v2' and package names in the command: `_call_brew('info', '--json=v2', *options, *names)`.
    *   Return a version string for a single package name or a dict mapping names to version strings for multiple names.
    *   Format version as '{stable_version}_{revision}' for formulae with revision >= 1 and bottle true; otherwise, return stable version.
    *   Retrieve version from 'version' field for casks.
    *   Call `refresh_db()` once by default when `refresh` kwarg is True.

*   Update `remove` function in `salt/modules/mac_brew_pkg.py`:
    *   Accept an optional `options` parameter (list of strings, default None).
    *   Insert `options` between 'uninstall' and package names in the command: `_call_brew('uninstall', *options, *packages)`.

*   Update `info_installed` function in `salt/modules/mac_brew_pkg.py`:
    *   Forward `options` keyword argument to the brew info command.
    *   Construct command with `options`: `_call_brew('info', '--json=v2', *options, *names)`.

*   Update `list_upgrades` function in `salt/modules/mac_brew_pkg.py`:
    *   Accept an optional `options` parameter (list of strings, default None).
    *   Append `options` after '--json=v2' in the command: `_call_brew('outdated', '--json=v2', *options)`.

*   Update `list_pkgs` function in `salt/modules/mac_brew_pkg.py`:
    *   Include formula aliases as separate entries in the returned package dict.
    *   Map each alias to the same installed version as the primary formula name.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.