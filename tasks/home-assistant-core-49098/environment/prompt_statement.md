I'm working on making Home Assistant safer when stopping or restarting the system. Right now, if someone triggers a stop or restart while the database is in the middle of a migration, the system just goes ahead and shuts down — which can corrupt the database. I need the stop and restart services to check whether a migration is currently in progress and refuse to execute if one is, raising a clear error explaining why.

I also need a helper that other parts of the system can use to check migration status. This helper should be safe to call even when the recorder component isn't loaded — in that case, it should just return false. When the recorder is loaded, it should report the actual migration state.

For the restart service specifically, it should also keep validating the configuration before restarting and raise an error if the configuration is invalid. The stop service, however, does not need to check configuration validity — only the migration check applies there.

One more thing: both the stop and restart actions should happen with a short asynchronous delay rather than firing immediately. And when the restart is triggered remotely via the WebSocket interface, it should be called in a blocking fashion.
