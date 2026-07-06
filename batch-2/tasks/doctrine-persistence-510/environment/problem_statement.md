## Description

There are several related improvements needed in the PHP-based metadata mapping drivers and the persistence registry.

**PHP Mapping Driver — require Closures:** Currently, PHP mapping files work by executing code directly in a shared scope, relying on the metadata variable being implicitly available. This approach is fragile. Mapping files should instead be required to return a closure that explicitly receives the metadata object as its parameter. If a file does not follow this pattern, a clear and descriptive error should be raised so the developer knows exactly what the file should do.

**Static PHP Driver — public API cleanup:** The static PHP driver exposes more public methods than it should. Its public interface should be trimmed down to only the essential methods (construction, listing all class names, checking for transient classes, and loading metadata for a class). Additionally, the driver should support accepting a pre-built class list object as an alternative to specifying file system directory paths, giving callers more flexibility.

**Registry — optional proxy interface:** The persistence registry currently requires a proxy interface class name to always be provided. There are valid use cases where no proxy support is needed, so this parameter should be made optional. When it is not set, the registry should still correctly resolve managers for regular entity classes, while returning nothing for proxy and anonymous classes.

## Expected Behavior

- PHP mapping files that do not return a closure produce a descriptive error message including the file path
- PHP mapping files that return a valid closure have it invoked with the metadata object as the argument
- The static PHP driver has a minimal, well-defined public API with exactly four public methods
- The static PHP driver can be initialized with either directory paths or a class list object
- The persistence registry can be configured without a proxy interface (null), and still correctly resolves entity managers for managed entity classes while returning null for proxy and anonymous classes

## Why This Matters

These changes make the mapping API more explicit and predictable, reduce accidental coupling to implicit global state in mapping files, and allow the registry to be used in environments where proxy classes are not employed.
