Fix the two bugs in the `virt.update()` function within the virtualization module to ensure correct behavior when updating virtual machine configurations. Address the unnecessary UEFI redefinition and ensure serial and console device updates are correctly applied.

*   Modify `virt.update()` to handle UEFI boot updates:
    *   When `boot={'efi': True}` is passed and the domain already has a UEFI pflash loader configured, ensure the function returns a dictionary with `'definition': False` and does not call `defineXML` on the connection.
    *   Ensure the internal EFI-handling logic only marks the definition as changed when `efi=True` and no loader exists. Set `firmware='efi'` only in this case and return `True`.

*   Correct the handling of serial and console device updates in `virt.update()`:
    *   Ensure the function applies changes from the `serials` and `consoles` parameters to the domain XML.
    *   Return a dictionary with `'definition': True` when serial or console devices are updated.
    *   Include `'disk'` and `'interface'` keys in the return dictionary, each with appropriate sub-keys even if no disk or NIC changes were requested.

*   Ensure the internal functions for serial/console device updates reference the correct parameter names:
    *   Use `'serials'` and `'consoles'` when building the set of device changes to prevent silent skipping of updates.

*   Ensure `virt.update()` does not raise exceptions when only `serials` and/or `consoles` parameters are provided:
    *   Exclude `'mem'` or `'cpu'` keys from the return value unless those parameters are explicitly provided.

*   Update the `_handle_efi_param` function:
    *   Return `False` without modifying the XML when `efi=True` and the domain's `<os>` element already contains a `<loader>` element.
    *   Set `firmware="efi"` and return `True` when `efi=True` and no loader is present.
    *   Remove loader and nvram elements and return `True` when `efi=False` and a loader is present.
    *   Raise `SaltInvocationError` for non-boolean `efi` values.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.