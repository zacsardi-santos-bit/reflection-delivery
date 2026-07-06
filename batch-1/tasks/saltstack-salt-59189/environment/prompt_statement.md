I've found two bugs in the virtual machine update function in the virtualization module that I need to fix.

The first bug is that when a VM already has UEFI firmware configured with explicit loader and variable-store file paths, calling the update function with the "use EFI automatically" option reports a definition change and re-submits the domain to the hypervisor — even though the VM is already correctly configured for UEFI. The update should detect that no change is needed and return without redefining the domain.

The second bug is that when I pass serial port or console device updates to the update function, those changes are silently ignored. It seems like the function has a parameter naming mismatch somewhere internally that causes the serial and console device lists to never reach the code that processes device changes. The function should apply the serial/console changes to the domain XML and return a result indicating the definition was updated, including the standard disk and interface change fields in the return value (even if no disk or NIC changes were made).

Can you fix both of these issues in the virtualization module?
