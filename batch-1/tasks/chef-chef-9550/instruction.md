Implement a reliable detection method for Xcode Command Line Tools installation by reading the system's installation history file, and add functionality to query the available Command Line Tools package label from the software update service.

*   Update the `xcode_cli_installed?` method:
    *   Implement it inside the `action_class` block in `lib/chef/resource/build_essential.rb`.
    *   Open `/Library/Receipts/InstallHistory.plist` using `File.open` with the path and 'r' mode.
    *   Parse the plist content to determine if any entry's display name contains "Command Line Tools".
    *   Return `true` if such an entry exists, otherwise return `false`.

*   Add the `xcode_cli_package_label` method:
    *   Implement it inside the `action_class` block in `lib/chef/resource/build_essential.rb`.
    *   Execute the `softwareupdate` CLI with the `--list` flag and parse its `stdout` output.
    *   For pre-macOS 10.15, identify the package as '   * Command Line Tools (macOS <version>) for Xcode-<ver>' and return the label string without leading whitespace or asterisk.
    *   For macOS 10.15+, identify the package as '* Label: Command Line Tools for Xcode-<ver>' and return the label portion after 'Label: '.
    *   Return `nil` if no Command Line Tools entry is found in the output.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.