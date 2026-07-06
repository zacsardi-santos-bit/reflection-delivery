Update the Flink packaged program infrastructure to support a configurable class loading order. Ensure that the class loading strategy respects the Flink configuration, allowing operators to choose between child-first and parent-first class loading. The default behavior should remain child-first unless otherwise specified.

*   Modify the `JarListHandler` class:
    *   Update the constructor to accept a `Configuration` object as a parameter, positioned between the `File jarDir` and `Executor executor` parameters.
    *   Store this `Configuration` and use it when creating `PackagedProgram` instances during jar listing.
    *   Location: `flink-runtime-web/src/main/java/org/apache/flink/runtime/webmonitor/handlers/JarListHandler.java`
    *   Signature: `JarListHandler(GatewayRetriever<? extends RestfulGateway> leaderRetriever, Time timeout, Map<String, String> responseHeaders, MessageHeaders<EmptyRequestBody, JarListInfo, EmptyMessageParameters> messageHeaders, CompletableFuture<String> localAddressFuture, File jarDir, Configuration configuration, Executor executor)`

*   Update the `PackagedProgram` class:
    *   Add a constructor overload with the signature: `PackagedProgram(File jarFile, List<URL> classpaths, @Nullable String entryPointClassName, Configuration configuration, String... args) throws ProgramInvocationException`.
    *   Ensure the existing constructor without `Configuration` delegates to this new one with a default `Configuration`.
    *   Location: `flink-clients/src/main/java/org/apache/flink/client/program/PackagedProgram.java`

*   Modify the `buildUserCodeClassLoader` method in the `JobWithJars` class:
    *   Update the method to accept a `Configuration` as a fourth parameter.
    *   Use the `classloader.resolve-order` setting from the `Configuration` to determine the class loading strategy.
    *   Default to child-first class loading when no resolve order is specified.
    *   Apply parent-first class loading when the configuration specifies 'parent-first'.
    *   Location: `flink-clients/src/main/java/org/apache/flink/client/program/JobWithJars.java`
    *   Signature: `buildUserCodeClassLoader(List<URL> jars, List<URL> classpaths, ClassLoader parent, Configuration configuration) -> ClassLoader`

*   Ensure the `ClassLoadingPolicyProgram` test class verifies the class loading policy:
    *   Accept two command-line arguments: the name of a resource to locate and the expected parent directory name.
    *   Throw `RuntimeException` if the resource is found in a different directory than expected.
    *   Location: `flink-tests/src/test/java/org/apache/flink/test/classloading/jar/ClassLoadingPolicyProgram.java`
    *   Signature: `main(String[] args) throws Exception`

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.