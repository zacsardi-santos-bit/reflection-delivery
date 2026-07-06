Implement a Rust module to support Linux's filesystem event monitoring interface. Create a safe, idiomatic API that allows developers to watch directories for changes like file creation, renaming, and opening. Ensure the module works on Linux and Android targets.

*   Add the `nix::sys::inotify` module at `src/sys/inotify.rs`.
    *   Conditionally compile this module for Linux and Android targets.
    *   Export the module publicly from `src/sys/mod.rs`.

*   Implement the `Inotify` struct with the following methods:
    *   `init(flags: InitFlags) -> Result<Inotify>`: Create a new inotify file descriptor with the specified flags.
    *   `add_watch<P: NixPath>(&self, path: &P, mask: AddWatchFlags) -> Result<WatchDescriptor>`: Register a path for monitoring with the specified event mask.
    *   `read_events(&self) -> Result<Vec<InotifyEvent>>`: Read all available inotify events. If initialized with `IN_NONBLOCK` and no events are available, return `Err(Error::Sys(Errno::EAGAIN))`.

*   Define the `InotifyEvent` struct with the following public fields:
    *   `wd: WatchDescriptor`: The watch descriptor that triggered this event.
    *   `mask: AddWatchFlags`: Bitfield describing the type of event (e.g., `IN_CREATE`, `IN_OPEN`).
    *   `cookie: u32`: A value linking related events (e.g., `IN_MOVED_FROM` and `IN_MOVED_TO` share the same cookie).
    *   `name: Option<OsString>`: The filename within the watched directory, if applicable.

*   Implement the `WatchDescriptor` struct as an opaque handle returned by `Inotify::add_watch`.

*   Define the `InitFlags` bitflags type with at least:
    *   `IN_NONBLOCK`: Sets the inotify file descriptor to non-blocking mode.
    *   `IN_CLOEXEC`: Sets close-on-exec on the file descriptor.

*   Define the `AddWatchFlags` bitflags type including at minimum:
    *   `IN_ACCESS`, `IN_MODIFY`, `IN_ATTRIB`, `IN_CLOSE_WRITE`, `IN_CLOSE_NOWRITE`, `IN_OPEN`, `IN_MOVED_FROM`, `IN_MOVED_TO`, `IN_CREATE`, `IN_DELETE`, `IN_DELETE_SELF`, `IN_MOVE_SELF`, `IN_UNMOUNT`, `IN_Q_OVERFLOW`, `IN_IGNORED`, `IN_CLOSE`, `IN_MOVE`, `IN_ONLYDIR`, `IN_DONT_FOLLOW`, `IN_ISDIR`, `IN_ONESHOT`, `IN_ALL_EVENTS`.

*   Ensure `read_events` returns events in the correct sequence:
    *   For file creation, include at least one event with `mask == IN_CREATE` and `name == Some(OsString::from(filename))`.
    *   For file creation and renaming, return exactly 5 events in order: `IN_CREATE`, `IN_OPEN`, `IN_CLOSE_WRITE`, `IN_MOVED_FROM`, `IN_MOVED_TO`. Ensure `IN_MOVED_FROM` and `IN_MOVED_TO` share the same `cookie` value.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.