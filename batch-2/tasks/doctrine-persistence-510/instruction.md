I'm working on improving several parts of the PHP metadata mapping system and the persistence registry.

*   The PHPDriver must require PHP mapping files to return a Closure. If the included mapping file does not return a Closure, the driver must throw a MappingException with the exact message: 'The PHP mapping file "{file_path}" must return a Closure that receives the ClassMetadata instance as argument.' where {file_path} is the absolute path to the mapping file.

*   When a PHP mapping file returns a valid Closure, the PHPDriver must invoke it with the ClassMetadata instance as its sole argument.

*   If the Closure returned by a mapping file accesses an unavailable object context (e.g. references an unbound $this) or uses class-level static access in an invalid scope, the resulting PHP Error must propagate naturally from PHPDriver without being caught or wrapped.

*   The StaticPHPDriver must expose exactly four public methods: __construct, getAllClassNames, isTransient, and loadMetadataForClass. No additional public methods should be present on the class.

*   The StaticPHPDriver constructor must accept either an array of file system paths or a ClassLocator object (such as ClassNames from Doctrine\Persistence\Mapping\Driver\ClassNames) as its single argument.

*   When StaticPHPDriver is constructed with a ClassLocator object, getAllClassNames() must return exactly the class names provided by that locator (e.g. constructing with ClassNames([Entity::class]) must return [Entity::class] from getAllClassNames()).

*   The AbstractManagerRegistry constructor must accept a nullable value (string|null) for the proxy interface name parameter. Passing null must be valid and must not cause an error during construction.

*   When the proxy interface name is null, getManagerForClass must still return an ObjectManager instance for regular managed entity classes.

*   When the proxy interface name is null, getManagerForClass must return null for named proxy classes (i.e. classes that are proxies but cannot be resolved to a managed entity without proxy interface information).

*   When the proxy interface name is null, getManagerForClass must return null for anonymous classes regardless of their parent class hierarchy.


*   Interface details: Type: Class
Name: PHPDriver
Location: src/Persistence/Mapping/Driver/PHPDriver.php
Description: Loads entity metadata from PHP files. The PHP mapping file must return a Closure that accepts a ClassMetadata instance as its sole argument. If the file does not return a Closure, a MappingException is thrown with the message: 'The PHP mapping file "{absolute_file_path}" must return a Closure that receives the ClassMetadata instance as argument.' The Closure is invoked with the ClassMetadata instance; any PHP Errors from the Closure (e.g. accessing unbound $this or invalid static scope) propagate naturally.
Signature: loadMetadataForClass(string $className, ClassMetadata $metadata): void

Type: Class
Name: StaticPHPDriver
Location: src/Persistence/Mapping/Driver/StaticPHPDriver.php
Description: Loads entity metadata by calling a static loadMetadata method on entity classes. Must expose exactly four public methods: __construct, getAllClassNames, isTransient, loadMetadataForClass. The constructor must accept either an array of file system paths or a ClassLocator object (e.g. Doctrine\Persistence\Mapping\Driver\ClassNames). When constructed with a ClassLocator, getAllClassNames() returns the class names from that locator.
Signature: __construct(array|ClassLocator $paths)
Signature: getAllClassNames(): array
Signature: isTransient(string $className): bool
Signature: loadMetadataForClass(string $className, ClassMetadata $metadata): void

Type: Class
Name: AbstractManagerRegistry
Location: src/Persistence/AbstractManagerRegistry.php
Description: Abstract base class for persistence manager registries. The proxy interface name constructor parameter must accept null. When null is passed, getManagerForClass returns an ObjectManager for regular managed entities, and returns null for proxy classes and anonymous classes.
Signature: __construct(string $name, array $connections, array $managers, string $defaultConnection, string $defaultManager, string|null $proxyInterfaceName)
Signature: getManagerForClass(string $class): ObjectManager|null


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.