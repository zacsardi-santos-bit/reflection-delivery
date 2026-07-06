Implement the missing data-access and manipulation methods for the `Fluent` class in the Hyperf support package. Ensure that the class can handle type-safe retrieval, dot-notation attribute setting, and dynamic macro registration.

*   Implement `set($key, $value): static` to store values using dot notation, affecting the correct nested position, and return the `Fluent` instance for chaining.
*   Implement `fill($attributes): static` to merge additional attributes into the container, updating `getAttributes()` and `all()`, and return the `Fluent` instance for chaining.
*   Implement `scope($key, $default = null): static` to return a new `Fluent` instance wrapping the value at the given dot-notation path, using the default if the key is missing.
*   Implement `all($keys = null): array` to return all attributes as an array, or a subset array of specified keys.
*   Implement `static macro($name, $callable): void` to allow dynamic registration of custom methods, ensuring they can access `fill()` and `all()` on instances.
*   Implement `string($key, $default = null): \Hyperf\Stringable\Stringable` to return a `Stringable` instance, casting numeric values to strings and returning an empty string for null or unknown keys.
*   Implement `boolean($key = null, $default = false): bool` to return a native PHP boolean, interpreting specific strings and values as true or false.
*   Implement `integer($key, $default = 0): int` to return a native PHP integer, using `intval()` and returning 0 for null values or unknown keys.
*   Implement `float($key, $default = 0.0): float` to return a native PHP float, using `floatval()` and returning 0.0 for null values or unknown keys.
*   Implement `array($key = null): array` to return all attributes as an array, a single key's value wrapped in an array, or a subset of specified keys.
*   Implement `collect($key = null): \Hyperf\Collection\Collection` to return a `Collection` instance, handling single keys, arrays of keys, or all attributes.
*   Implement `date($key, $format = null, $tz = null): ?\Carbon\Carbon` to return a `Carbon` instance or null, parsing dates with or without a format, and throwing an `InvalidArgumentException` for invalid cases.
*   Implement `enum($key, $enumClass): mixed` to return a backed enum instance using `tryFrom()`, or null for invalid cases.
*   Implement `enums($key, $enumClass): array` to return an array of backed enum instances, filtering out nulls and handling invalid cases.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.