## Description

When a developer runs a Flutter command without specifying a target device and multiple connected devices are available, the tool should help them choose which device to use. Currently, the tool automatically picks the single ephemeral device (such as a connected phone) when it is the only one of that kind among multiple devices. However, when there are multiple ephemeral devices, multiple non-ephemeral devices, or a mix that can't be automatically narrowed down to one, there is no mechanism to ask the user for their preference — the tool either returns all of them or fails.

## Expected Behavior

- When exactly one ephemeral device exists among multiple devices, it is automatically selected (existing behavior preserved).
- When multiple devices remain after the auto-selection attempt, the tool should display a numbered list of the available devices and prompt the user to pick one by entering the corresponding number.
- The user selection prompt should only appear in interactive terminal sessions, not in CI or non-interactive environments.
- After the user makes a selection, the command proceeds targeting only that chosen device.

## Why This Matters

Developers who have multiple Android devices connected simultaneously, or who run multiple emulators or desktop targets at once, currently have no streamlined way to choose a target device without always passing an explicit flag. An interactive prompt makes the workflow significantly smoother for everyday use.
