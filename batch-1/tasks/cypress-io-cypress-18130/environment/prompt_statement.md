I'd like Cypress to warn users when it detects that they're running on a 32-bit Windows system. Right now, when someone opens a project on such a machine, there's no indication that this platform is being deprecated. Users may not realize their configuration will lose support until it suddenly stops working in a future release.

I want the project creation flow to check whether the current operating system is 32-bit Windows — specifically, both the platform and the CPU architecture need to match — and if so, call the warning callback that is already available as part of the options passed to the create step. The warning message delivered through that callback should make it clear that a 32-bit build is being used.

The warning should only fire for 32-bit Windows. Other platforms and 64-bit Windows should not be affected.
