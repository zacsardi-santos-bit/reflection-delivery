Implement a fix for the UPnP port mapping code to handle devices that advertise only unsupported service types. Ensure the system gracefully handles such cases without crashing and reports the presence of UPnP-capable devices correctly.

*   Update the `tryUPnPPortmapWithDevice` function in `net/portmapper/upnp.go`:
    *   Check if the selected UPnP service client is nil.
    *   If nil, return an error with the message "no supported UPnP clients" instead of proceeding.
    *   Ensure the function signature is `(c *Client) tryUPnPPortmapWithDevice(ctx context.Context, logf logger.Logf, loc *url.URL, rootDev *goupnp.RootDevice, ...) (netip.AddrPort, *Lease, error)`.

*   Modify the `selectBestService` function in `net/portmapper/upnp.go`:
    *   Allow it to return `(nil, nil)` when no supported service type is found.
    *   Ensure callers handle this nil return without attempting to dereference it.
    *   Maintain the function signature as `selectBestService(logf logger.Logf, rootDev *goupnp.RootDevice, loc *url.URL) (upnpClient, error)`.

*   Ensure that network probing:
    *   Detects UPnP presence on the network even if all discovered services are unsupported.
    *   Sets the UPnP flag to true in the probe result in such cases.

*   Handle Huawei-style routers:
    *   Ensure the UPnP client selection returns nil without crashing when only DSL Forum or vendor-specific service types are exposed.
    *   Log output for such devices should be an empty string, indicating no service announcement.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.