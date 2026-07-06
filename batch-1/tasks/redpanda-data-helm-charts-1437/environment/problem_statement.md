## Description

The console Helm chart currently defines its Kubernetes Service resource entirely through raw template logic inline in the template file. There is no Go-level function representing the Service, which means the Service generation cannot be unit tested as Go code and cannot benefit from the type safety and testability that the rest of the chart's Go-based template helpers provide.

## Expected Behavior

- A new Go function should be added to the console chart package that constructs and returns a typed Kubernetes Service object from chart values.
- The function should derive the service name from the release name using the chart's existing naming convention, populate the service type, port, and annotations from chart values, and return a properly constructed service object.
- This function should then be used by the Helm template layer so the service template is backed by a tested, typed Go function rather than inline template logic.

## Why This Matters

Moving the Service definition into a Go function allows developers to write direct unit tests against the service construction logic, catch regressions early, and ensure the service is always built consistently according to chart values. It also aligns the Service resource with how other resources in the chart are already being managed.
