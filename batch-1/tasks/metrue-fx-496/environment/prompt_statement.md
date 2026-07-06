I'm working on a tool that provisions Docker environments on remote hosts over SSH, and I'd like to split the provisioning logic into separate implementations for macOS and Linux hosts. Right now there's a single shared implementation, but the two platforms really need different behavior.

For macOS, I don't want the tool to try to auto-install Docker if it isn't running — instead, it should check whether Docker is on the system path and, if not, return an error telling the user to install it manually. For Linux, the existing auto-install behavior makes sense, but I'd like to add an intermediate check to see if Docker is accessible on the path before deciding to install it.

Both implementations need to handle SSH connectivity properly: before sending any remote command, they should verify the connection is reachable and return an error immediately if it isn't. When the required agent process isn't running on the remote host, both should attempt to start it automatically.

I'd like to organize these as separate packages — one for macOS hosts and one for Linux hosts — both satisfying the same provisioner interface. The macOS package doesn't exist yet and needs to be created from scratch.
