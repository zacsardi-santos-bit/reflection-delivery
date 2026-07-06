I'm working on the Flutter tools environment checker for macOS.

*   When CocoaPods is not installed, the CocoaPodsValidator must report a ValidationType.partial result (not ValidationType.missing).

*   When CocoaPods is not installed, the validation message must have type ValidationMessageType.hint (not ValidationMessageType.error).

*   The validation message for a missing CocoaPods installation must still contain the text 'CocoaPods not installed' and a reference to 'getting-started.html#installation'.

*   The validation result for a missing CocoaPods installation must contain exactly one message.


*   Interface details: Type: Class
Name: CocoaPodsValidator
Location: packages/flutter_tools/lib/src/macos/cocoapods_validator.dart
Description: Validates the CocoaPods installation as part of the Flutter doctor check. The validate() method must return a ValidationResult with ValidationType.partial (not ValidationType.missing) and a ValidationMessage with type ValidationMessageType.hint (not ValidationMessageType.error) when CocoaPods is not installed (CocoaPodsStatus.notInstalled).
Signature: validate() -> Future<ValidationResult>


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.