Implement a new Spring Security module to handle compromised passwords during user registration. Create a standalone Spring Boot application with an in-memory database that exposes a user creation endpoint. Ensure the application checks for compromised passwords and provides a reusable bean-validation constraint.

*   Create the `UserCreationRequestDto` class in `com.baeldung.security.dto`.
    *   Include public getters and setters for `emailId` and `password` fields.
    *   Annotate the `password` field with a custom bean-validation constraint that produces the message: "The provided password is compromised and cannot be used" when violated.

*   Develop a custom annotation `NotCompromised` in `com.baeldung.security.validation`.
    *   Set the default message to "The provided password is compromised and cannot be used."
    *   Include standard `groups()` and `payload()` elements.
    *   Reference `CompromisedPasswordValidator` as the `validatedBy` class.

*   Implement `CompromisedPasswordValidator` in `com.baeldung.security.validation`.
    *   Implement `ConstraintValidator<NotCompromised, String>`.
    *   Use constructor injection to receive a `CompromisedPasswordChecker` bean.
    *   Return `false` from `isValid` when the password is compromised.

*   Create a POST `/users` endpoint in `com.baeldung.security.controller.UserController`.
    *   Accept a JSON body with `emailId` and `password`.
    *   Return HTTP 200 OK when the password is not compromised.
    *   Return HTTP 400 Bad Request with a ProblemDetail JSON body when the password is compromised.
        *   Include a `status` field with value `400`.
        *   Include a `detail` field with the message: "The provided password is compromised and cannot be used."
    *   Ensure the endpoint is publicly accessible without authentication.
    *   Disable CSRF protection and use stateless session management.

*   Define the Spring Boot application entry point class `Application` in `com.baeldung.security`.
    *   Annotate it as a Spring Boot application.

*   Set up the Maven module `spring-security-compromised-password`.
    *   Register it as a child module in `spring-security-modules/pom.xml`.
    *   Use `parent-boot-3` as the parent POM.
    *   Include dependencies for Spring Boot Web, Security, Validation, Data JPA, H2 (runtime scope), and Lombok.
    *   Configure an in-memory H2 datasource in `application.properties`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.