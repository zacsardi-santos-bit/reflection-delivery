I'm working on adding version pinning support for registry templates in Pulumi.

*   The ResolveTemplateFromName function must accept a context, a Registry, a string identifier, and an optional semver.Version pointer. It must return apitype.TemplateMetadata and an error.

*   When the identifier is three slash-separated parts (source/publisher/name), ResolveTemplateFromName must call GetTemplate directly with those parts and the provided version. It must return ErrNotFound (checkable via errors.Is) when the template is not found.

*   When the identifier is two slash-separated parts (publisher/name), ResolveTemplateFromName must first try source='private', and if that returns ErrNotFound, fall back to source='pulumi'. It must return ErrNotFound if neither source has the template.

*   When the identifier is a single part (name), ResolveTemplateFromName must call ListTemplates with ListTemplatesOptions{Name: name} to discover the source and publisher. If version is non-nil, it must then call GetTemplate with the discovered coordinates and version. If the versioned lookup fails with ErrNotFound, the error message must contain the string 'version <version-number> was not found' (e.g. 'version 99.0.0 was not found').

*   When the identifier contains four or more slash-separated parts, ResolveTemplateFromName must return an InvalidIdentifierError. The error must be detectable via errors.As(err, &InvalidIdentifierError{}).

*   When the template is not found in any case (other than the single-part-with-version case), the error must wrap ErrNotFound so that errors.Is(err, ErrNotFound) returns true.

*   The template source in the templates package must support an '@version' suffix in all supported URL forms: source/publisher/name@X.Y.Z, registry://templates/source/publisher/name@X.Y.Z, publisher/name@X.Y.Z, and name@X.Y.Z.

*   When a version suffix is present in the template URL, the registry's GetTemplate API must be called with the parsed semver version rather than using ListTemplates for resolution.

*   When a versioned template lookup fails because the registry returns a not-found error, the returned error message must contain the string "version 'X.Y.Z' not found" (with single quotes around the version number, e.g. "version '2.0.0' not found").

*   When a template URL includes a version suffix and the resolved template is VCS-backed (e.g. backed by GitHub), the operation must fail with an error whose message contains both 'VCS-backed' and 'does not support specific versions'.

*   Template objects returned from the templates source must expose Name() returning the template name, DisplayName() returning the name and publisher in the format 'name [publisher]', and Description() returning the template description.


*   Interface details: Type: Function
Name: ResolveTemplateFromName
Location: sdk/go/common/registry/resolve.go (or a new file within sdk/go/common/registry/)
Signature: ResolveTemplateFromName(ctx context.Context, reg Registry, name string, version *semver.Version) (apitype.TemplateMetadata, error)
Description: Resolves a template from the registry by a human-readable identifier and optional version. The name parameter may be a single-part name, a two-part "publisher/name", or a three-part "source/publisher/name". If version is non-nil, the specific version is fetched via GetTemplate; otherwise the latest is used. Returns ErrNotFound (checkable via errors.Is) when the template does not exist. Returns InvalidIdentifierError (checkable via errors.As) when the identifier has more than 3 slash-separated parts. For single-part with a version that isn't found, the error message must contain "version <X.Y.Z> was not found".

Resolution rules:
- Three-part (source/publisher/name): calls Registry.GetTemplate(ctx, source, publisher, name, version) directly
- Two-part (publisher/name): tries source="private" first; if ErrNotFound, tries source="pulumi"
- Single-part (name): calls Registry.ListTemplates(ctx, ListTemplatesOptions{Name: name}) to discover source/publisher; if version != nil, calls GetTemplate with discovered coordinates and version
- Four or more parts: immediately returns InvalidIdentifierError

Type: Error Type
Name: InvalidIdentifierError
Location: sdk/go/common/registry/ (same package as ResolveTemplateFromName)
Description: A concrete error type returned when a template identifier has more than three slash-separated parts. Must be checkable via errors.As(err, &InvalidIdentifierError{}).

Note on existing Registry interface:
The Registry interface (in sdk/go/common/registry) must include a GetTemplate method with signature:
  GetTemplate(ctx context.Context, source, publisher, name string, version *semver.Version) (apitype.TemplateMetadata, error)
and a ListTemplates method with signature:
  ListTemplates(ctx context.Context, opts ListTemplatesOptions) iter.Seq2[apitype.TemplateMetadata, error]

Note on ListTemplatesOptions:
ListTemplatesOptions (in sdk/go/common/registry) must include a Name string field used for filtering templates by name.

Note on template URL versioning (pkg/cmd/pulumi/templates):
The internal newImpl function must be updated to parse @version suffixes from template URLs (all formats: source/publisher/name@X.Y.Z, registry://templates/source/publisher/name@X.Y.Z, publisher/name@X.Y.Z, name@X.Y.Z). When a version suffix is present:
- GetTemplate is called with the parsed version, not ListTemplates
- If the registry returns a not-found error, the error message must contain "version 'X.Y.Z' not found" (with single quotes around the version)
- If the resolved template is VCS-backed and a version was specified, the error message must contain "VCS-backed" and "does not support specific versions"


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.