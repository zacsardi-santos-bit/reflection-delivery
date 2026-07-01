I'm working on a disk usage reporting utility and noticed that the "available" column in its output is showing the wrong number. It's currently displaying the total free space on a filesystem — which includes space that's reserved for the system administrator and not actually usable by normal users — instead of the space that's genuinely available to ordinary users.

On Linux, most filesystems reserve a portion of their blocks for privileged processes. So the "free" and "available" figures can differ significantly. The standard system disk usage tool has always shown the user-available figure in its "Avail" column, and our reimplementation should match that behavior.

Right now, if I have a filesystem with, say, 750 free blocks but only 600 available to unprivileged users, the output incorrectly shows 750 in the available column. It should show 600. Can you fix the code so that the available column uses the user-available block count rather than the total free block count?
