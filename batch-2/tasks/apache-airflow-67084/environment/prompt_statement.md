I'm hitting a wall with the Airflow nav bar and how it handles plugin links. Right now the moment two or more plugins are registered, every one of them gets shoved into a shared "Plugins" dropdown submenu, so there's no way for a plugin author to keep their link visible up top. It always ends up hidden behind an extra click once a second plugin shows up, which is annoying if you've got an important integration or tool you want people to reach immediately.

What I want is an optional flag on plugin definitions (something a plugin author can set) that, when it's on, always renders that plugin's nav link directly on the main toolbar no matter how many other plugins exist. Basically a way to "promote" a link to the top level.

The non-flagged plugins should keep behaving exactly like today. So after you pull the flagged ones out, if two or more non-flagged ones remain they still collapse into the existing submenu, but if only one non-flagged plugin is left it should also appear directly on the toolbar so we don't end up with a silly one-item dropdown.

Also on the API side, the REST response for plugin items needs to expose this new flag explicitly, and it should default to false for any plugin that hasn't opted in. Existing plugins keep working as-is, nothing changes for them unless they set the flag.
