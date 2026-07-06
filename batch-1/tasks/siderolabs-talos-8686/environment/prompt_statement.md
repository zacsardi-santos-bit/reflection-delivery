I'm working on the Talos project and we're starting the 1.8 release cycle. We have a configuration encoding stability test that tracks how machine configurations are serialized for each supported version. Right now it covers versions 1.3 through 1.7 — each version has golden reference YAML files that the test compares against to detect unintentional encoding changes.

I need to extend this stability coverage to version 1.8. The version identifier for 1.8 already exists in the codebase; the missing pieces are adding it to the stability test's version list and creating the corresponding golden reference files. These reference files should cover both controlplane and worker node configurations, in both base form and with commonly used overrides applied.

Can you help wire up version 1.8 in the stability test and add the required reference data files so the test suite passes?
