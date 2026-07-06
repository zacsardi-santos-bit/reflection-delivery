## Description

The OCI metrics CDI bean currently cannot be customized through subclassing. All of its key setup methods are package-private, which means that developers who need to adjust how OCI metrics are configured — for example, to use a different configuration key or to map alternate property names to metric settings — have no supported way to do so. There is also no way for application or library developers to supply their own logic for building or activating the OCI metrics integration.

Additionally, there is no explicit guarantee or test that the OCI metrics bean initializes itself only after the standard metrics CDI extension has fully completed its own setup. If the ordering is wrong, metrics may not be available when the OCI integration tries to use them.

## Expected Behavior

- Key lifecycle methods on the OCI metrics CDI bean should be accessible to subclasses (i.e., have protected visibility), so that library or application developers can extend and customize the integration.
- Subclasses should be able to override the method that determines which config key is used to look up OCI metrics settings, allowing alternate configuration hierarchies.
- Subclasses should be able to override the method that creates and initializes the metrics support builder, allowing custom config property mappings or alternate initialization logic.
- The method that activates metrics support should receive both the root config and the OCI-metrics-specific config node, giving overriding implementations full access to the configuration tree.
- The CDI bean should store the built metrics support object so callers can retrieve it after activation.
- The OCI metrics observer must have a higher priority than the standard metrics CDI extension's registration observer, ensuring correct initialization order.

## Why This Matters

Without extensibility, integrators are forced to copy-paste or re-implement large portions of the OCI metrics setup code. Making the bean properly extensible allows downstream libraries to provide drop-in customizations without forking the core implementation.
