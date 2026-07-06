Implement new configuration options in the Kotlin Misk server code generator to enhance customization of generated server code. Ensure the generator correctly declares JSON as a supported wire format.

*   Declare the following public static constants in `KotlinMiskServerCodegen`:
    *   `ACTION_PATH_PREFIX` for configuring a path prefix for API action routes.
    *   `GENERATE_STUB_IMPL_CLASSES` for controlling stub implementation class generation.
    *   `ADD_MODEL_MOSHI_JSON_ANNOTATION` for controlling Moshi JSON annotation on model classes.

*   Implement the following setter methods in `KotlinMiskServerCodegen`:
    *   `setActionPathPrefix(String actionPathPrefix)` to set the path prefix for API action routes.
    *   `setGenerateStubImplClasses(Boolean generateStubImplClasses)` to control stub implementation class generation.
    *   `setAddModelMoshiJsonAnnotation(Boolean addModelMoshiJsonAnnotation)` to control Moshi JSON annotation on model classes.

*   Update the feature set in `KotlinMiskServerCodegen`:
    *   Ensure `getFeatureSet().getWireFormatFeatures()` includes `WireFormatFeature.JSON` alongside `WireFormatFeature.PROTOBUF`.

*   Ensure option processing calls the appropriate setter methods:
    *   Call `setActionPathPrefix()` with the `ACTION_PATH_PREFIX` property's String value when set.
    *   Call `setGenerateStubImplClasses()` with the parsed Boolean value of the `GENERATE_STUB_IMPL_CLASSES` property when set.
    *   Call `setAddModelMoshiJsonAnnotation()` with the parsed Boolean value of the `ADD_MODEL_MOSHI_JSON_ANNOTATION` property when set.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.