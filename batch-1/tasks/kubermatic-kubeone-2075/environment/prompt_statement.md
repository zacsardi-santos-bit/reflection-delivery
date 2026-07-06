I need to add support for configuring the maximum number of pods per node in the host configuration for a Kubernetes cluster management tool. Right now, there's no way to specify this per-host pod limit — nodes just use whatever the default is. I'd like to add an optional field to the per-host configuration that lets operators set this value.

I also need validation: if someone sets this field, it has to be a positive number. Setting it to zero or a negative value doesn't make sense and should be caught early with a validation error. If the field is left unset, that should be fine — it just means the host uses its runtime default.

The validation logic for host configurations already exists — it just needs to be extended to also check the new maximum pod count field.
