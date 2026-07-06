I'm working on the Medplum AWS deployment tooling and need to add support for the cloud-native malware scanning service for S3 storage.

*   The `updateBucketPoliciesCommand` (invoked via `main`) must wrap each individual bucket policy update in a try/catch block so that a failure on one bucket does not prevent the other from being updated.

*   When the App bucket policy update fails, the command must log the error message `'Error updating App bucket policy: <error message>'` to console.error, continue to update the Storage bucket, and log `'Done'` upon completion.

*   When the Storage bucket policy update fails, the command must log the error message `'Error updating Storage bucket policy: <error message>'` to console.error, continue (App bucket update already completed), and log `'Done'` upon completion.

*   The `updateBucketPolicy` function must accept a fifth argument `options` with an optional `guarddutyMalwareProtection?: boolean` field (as part of `UpdateBucketPoliciesOptions`).

*   When `updateBucketPolicy` is called for the 'Storage' bucket with `options.guarddutyMalwareProtection` set to true, it must append a Deny policy statement with `Sid: 'GuardDutyMalwareProtectionReadGate'`, `Effect: 'Deny'`, `Action: ['s3:GetObject', 's3:GetObjectVersion']`, `Resource: 'arn:aws:s3:::<bucketName>/*'`, and `Condition: { StringNotEquals: { 's3:ExistingObjectTag/GuardDutyMalwareScanStatus': 'NO_THREATS_FOUND' } }` after the CloudFront Allow statement.

*   When `guarddutyMalwareProtection` is true and the bucket policy already contains a Deny statement with `Sid: 'GuardDutyMalwareProtectionReadGate'` that includes both `s3:GetObject` and `s3:GetObjectVersion` actions and the correct condition, no duplicate statement must be added.

*   When `guarddutyMalwareProtection` is true and the bucket policy contains a Deny statement that is missing one or more of the required actions (e.g., only `s3:GetObject` without `s3:GetObjectVersion`) or has a different Sid, a new complete `GuardDutyMalwareProtectionReadGate` Deny statement must be appended regardless.

*   The `updateBucketPolicy` function must throw an error with the message `'${friendlyName} bucket already has policy statement'` (e.g., `'App bucket already has policy statement'`) when the existing bucket policy already contains an Allow statement for the given OAI and bucket.

*   The CDK stack must accept a `guardDutyMalwareProtectionEnabled?: boolean` field in the infrastructure configuration and successfully synthesize a CloudFormation stack when this field is set to true and `clamScanEnabled` is false.


*   Interface details: Type: Function
Name: updateBucketPolicy
Location: packages/cli/src/aws/update-bucket-policies.ts
Signature: updateBucketPolicy(friendlyName: string, bucketResource: StackResource | undefined, distributionResource: StackResource | undefined, oaiResource: StackResource | undefined, options: UpdateBucketPoliciesOptions): Promise<void>
Description: Updates the S3 bucket policy for a given named bucket (e.g., 'App' or 'Storage'). Throws if the bucket already has an Allow statement. When options.guarddutyMalwareProtection is true and friendlyName is 'Storage', appends a GuardDuty Deny statement.

Type: Interface
Name: UpdateBucketPoliciesOptions
Location: packages/cli/src/aws/update-bucket-policies.ts
Description: Options for the bucket policy update command and the updateBucketPolicy function.
Fields:
  file?: string
  dryrun?: boolean
  guarddutyMalwareProtection?: boolean

Type: Interface
Name: MedplumInfraConfig
Location: packages/core/src/config.ts
Description: Medplum infrastructure configuration object. Must include the new guardDutyMalwareProtectionEnabled optional field.
Fields:
  guardDutyMalwareProtectionEnabled?: boolean
  clamscanEnabled?: boolean (deprecated)
  clamscanLoggingBucket?: string (deprecated)
  clamscanLoggingPrefix?: string (deprecated)


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.