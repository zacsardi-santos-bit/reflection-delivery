Remove version-specific branching logic from the iOS physical-device debugging setup and implement a consistent command sequence for all development tool versions. Add a workaround for an upstream bug causing breakpoints to fail to rearm by disabling asynchronous execution mode in the debugger.

*   Update the LLDB class:
    *   Modify the constructor to accept only `logger` and `processUtils` as required named parameters.
    *   Remove the `xcode` parameter from the constructor and ensure the class does not store or use a Xcode instance.
*   Update the IOSCoreDeviceLauncher class:
    *   Ensure the constructor does not require or accept a `xcode` parameter.
    *   When creating an LLDB instance, call `LLDB(logger: logger, processUtils: processUtils)` without passing `xcode`.
*   Revise the debugging session setup (attachAndStart):
    *   Use the command `breakpoint set --func-regex '^NOTIFY_DEBUGGER_ABOUT_RX_PAGES$'` without the `--auto-continue true` flag for setting breakpoints.
    *   After sending the breakpoint Python script command, send `script lldb.debugger.SetAsync(False)` to the debugger before the process attach command.
    *   Ensure the command sequence sent to the debugger stdin is as follows:
        1.  `device select <deviceId>`
        2.  `breakpoint set --func-regex '^NOTIFY_DEBUGGER_ABOUT_RX_PAGES$'`
        3.  `breakpoint command add --script-type python <breakpointId>`
        4.  Python script lines
        5.  `DONE`
        6.  `script lldb.debugger.SetAsync(False)`
        7.  `device process attach --pid <appProcessId>`
        8.  `process continue`
*   Update the detection of a successful session start:
    *   Match the text 'location added to breakpoint' to detect successful resumption.
    *   Discontinue using the pattern 'Process <pid> resuming' as a resumption signal.
*   Ensure that upon successful completion of attachAndStart:
    *   The function returns `true`.
    *   `isRunning` is set to `true`.
    *   `appProcessId` is set to the provided process ID.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.