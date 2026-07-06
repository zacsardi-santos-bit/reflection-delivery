Implement the necessary transformations in the AWS SDK V1-to-V2 migration tool to handle enum-related code patterns. Update enum constant references to match the new naming convention and rewrite getter methods to their string-returning equivalents. Ensure these changes are applied to SQS, SNS, and DynamoDB service models.

*   Create the `EnumCasingToV2` class:
    *   Place it in `v2-migration/src/main/java/software/amazon/awssdk/v2migration/EnumCasingToV2.java`.
    *   Extend `org.openrewrite.Recipe`.
    *   Implement a no-arg constructor.
    *   Convert V1 PascalCase enum constants to V2 SCREAMING_SNAKE_CASE.
    *   Ensure it functions in a two-recipe pipeline with `ChangeSdkType`, completing in 2 rewrite cycles.

*   Develop the `change-enum-getters.yml` YAML recipe:
    *   Locate it at `v2-migration/src/main/resources/META-INF/rewrite/change-enum-getters.yml`.
    *   Name the recipe `software.amazon.awssdk.v2migration.EnumGettersToV2`.
    *   Rename V1 getter methods to V2 string-returning equivalents:
        *   `com.amazonaws.services.sns.model.PhoneNumberInformation.getRouteType()` to `routeTypeAsString`.
        *   `com.amazonaws.services.sns.model.PhoneNumberInformation.getNumberCapabilities()` to `numberCapabilitiesAsStrings`.
        *   `com.amazonaws.services.sqs.model.ReceiveMessageRequest.getAttributeNames()` to `attributeNamesAsStrings`.
    *   Ensure it works in a three-recipe pipeline with `ChangeSdkType` and `NewClassToBuilder`, completing in 2 rewrite cycles.

*   Update the run-test script:
    *   Implement the `write_version_to_pom(pom_file, version)` function.
    *   Ensure `V2_VERSION` substitution is applied to both before and after POM files.
    *   Store the before POM path as `BEFORE_POM = os.path.join(TARGET_DIR_MAVEN, 'pom.xml')`.

*   Integration test requirements:
    *   Ensure `before/src/main/java/foo/bar/Enums.java` contains V1-style code with PascalCase enum references and V1 getter methods.
    *   Ensure `after/src/main/java/foo/bar/Enums.java` contains V2-style code with SCREAMING_SNAKE_CASE enum references and V2 getter methods.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.