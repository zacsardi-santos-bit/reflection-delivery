I'm working with cloud-nuke and noticed that tag-based inclusion filters don't work for a number of AWS resource types.

*   The listAccessAnalyzers function must pass the tags map from each analyzer's summary into the resource filter check, so that tag-based inclusion rules in config.ResourceType are honored when listing access analyzers.

*   The listACMCertificates function must retrieve tags for each certificate by calling ListTagsForCertificate on the client, convert them to a string map, and pass them into the resource filter check so that tag-based inclusion rules are applied.

*   The listACMPCA function must retrieve tags for each certificate authority by calling ListTags on the client, convert them to a string map, and pass them into the resource filter check so that tag-based inclusion rules are applied.

*   The listAppRunnerServices function must retrieve tags for each service by calling ListTagsForResource on the client using the service ARN, convert them to a string map, and include them in the resource filter check.

*   The listCloudfrontDistributions function must retrieve tags for each distribution by calling ListTagsForResource on the client using the distribution ARN, convert them to a string map, and include them in the resource filter check.

*   The listConfigServiceRules function must retrieve tags for each config rule by calling ListTagsForResource on the client using ConfigRuleArn, convert them to a string map, and include them in the resource filter check.

*   The listDataPipelines function must convert the Tags field on each pipeline description to a string map and include it in the resource filter check for tag-based inclusion rules.

*   The listDataSyncLocations function must retrieve tags for each location by calling ListTagsForResource on the client using the location ARN, convert them to a string map, and include them in the resource filter check.

*   The listDataSyncTasks function must retrieve tags for each task by calling ListTagsForResource on the client using the task ARN, convert them to a string map, and include them in the resource filter check.

*   The listEBApplications function must retrieve tags for each application by calling ListTagsForResource on the client using ApplicationArn, convert them to a string map, and include them in the resource filter check.

*   The listGuardDutyDetectors function must pass the Tags map from the GetDetector response for each detector into the resource filter check so that tag-based inclusion rules are honored.

*   The listSageMakerNotebookInstances function must retrieve tags for each notebook instance by calling ListTags on the client using NotebookInstanceArn, convert them to a string map, and include them in the resource filter check.

*   The listSageMakerDomains function must retrieve tags for each domain by calling ListTags on the client using DomainArn, convert them to a string map, and include them in the resource filter check.

*   The ACMAPI interface must include a ListTagsForCertificate method with the standard AWS SDK signature for the ACM service.

*   The ACMPCAAPI interface must include a ListTags method with the standard AWS SDK signature for the ACM PCA service.

*   The AppRunnerServiceAPI interface must include a ListTagsForResource method with the standard AWS SDK signature for the App Runner service.

*   The CloudfrontDistributionAPI interface must include a ListTagsForResource method with the standard AWS SDK signature for the CloudFront service.

*   The ConfigServiceRuleAPI interface must include a ListTagsForResource method with the standard AWS SDK signature for the Config Service.

*   The DataSyncLocationAPI interface must include a ListTagsForResource method with the standard AWS SDK signature for the DataSync service.

*   The DataSyncTaskAPI interface must include a ListTagsForResource method with the standard AWS SDK signature for the DataSync service.

*   The EBApplicationsAPI interface must include a ListTagsForResource method with the standard AWS SDK signature for the Elastic Beanstalk service.

*   The SageMakerNotebookInstancesAPI interface must include a ListTags method with the standard AWS SDK signature for the SageMaker service.

*   The SageMakerStudioAPI interface must include a ListTags method with the standard AWS SDK signature for the SageMaker service.


*   Interface details: Type: Interface
Name: ACMAPI
Location: aws/resources/acm.go
Description: Interface for ACM operations. Must be extended to include a method for retrieving certificate tags.
Signature: ListTagsForCertificate(ctx context.Context, params *acm.ListTagsForCertificateInput, optFns ...func(*acm.Options)) (*acm.ListTagsForCertificateOutput, error)

Type: Interface
Name: ACMPCAAPI
Location: aws/resources/acmpca.go
Description: Interface for ACM PCA operations. Must be extended to include a method for retrieving certificate authority tags.
Signature: ListTags(ctx context.Context, params *acmpca.ListTagsInput, optFns ...func(*acmpca.Options)) (*acmpca.ListTagsOutput, error)

Type: Interface
Name: AppRunnerServiceAPI
Location: aws/resources/apprunner_service.go
Description: Interface for App Runner operations. Must be extended to include a method for retrieving service tags.
Signature: ListTagsForResource(ctx context.Context, params *apprunner.ListTagsForResourceInput, optFns ...func(*apprunner.Options)) (*apprunner.ListTagsForResourceOutput, error)

Type: Interface
Name: CloudfrontDistributionAPI
Location: aws/resources/cloudfront_distribution.go
Description: Interface for CloudFront distribution operations. Must be extended to include a method for retrieving distribution tags.
Signature: ListTagsForResource(ctx context.Context, params *cloudfront.ListTagsForResourceInput, optFns ...func(*cloudfront.Options)) (*cloudfront.ListTagsForResourceOutput, error)

Type: Interface
Name: ConfigServiceRuleAPI
Location: aws/resources/config_service.go
Description: Interface for AWS Config Service rule operations. Must be extended to include a method for retrieving config rule tags.
Signature: ListTagsForResource(ctx context.Context, params *configservice.ListTagsForResourceInput, optFns ...func(*configservice.Options)) (*configservice.ListTagsForResourceOutput, error)

Type: Interface
Name: DataSyncLocationAPI
Location: aws/resources/datasync_location.go
Description: Interface for DataSync Location operations. Must be extended to include a method for retrieving location tags.
Signature: ListTagsForResource(ctx context.Context, params *datasync.ListTagsForResourceInput, optFns ...func(*datasync.Options)) (*datasync.ListTagsForResourceOutput, error)

Type: Interface
Name: DataSyncTaskAPI
Location: aws/resources/datasync_task.go
Description: Interface for DataSync Task operations. Must be extended to include a method for retrieving task tags.
Signature: ListTagsForResource(ctx context.Context, params *datasync.ListTagsForResourceInput, optFns ...func(*datasync.Options)) (*datasync.ListTagsForResourceOutput, error)

Type: Interface
Name: EBApplicationsAPI
Location: aws/resources/elastic_beanstalk.go
Description: Interface for Elastic Beanstalk application operations. Must be extended to include a method for retrieving application tags.
Signature: ListTagsForResource(ctx context.Context, params *elasticbeanstalk.ListTagsForResourceInput, optFns ...func(*elasticbeanstalk.Options)) (*elasticbeanstalk.ListTagsForResourceOutput, error)

Type: Interface
Name: SageMakerNotebookInstancesAPI
Location: aws/resources/sagemaker_notebook_instance.go
Description: Interface for SageMaker Notebook Instance operations. Must be extended to include a method for retrieving notebook instance tags.
Signature: ListTags(ctx context.Context, params *sagemaker.ListTagsInput, optFns ...func(*sagemaker.Options)) (*sagemaker.ListTagsOutput, error)

Type: Interface
Name: SageMakerStudioAPI
Location: aws/resources/sagemaker_studio.go
Description: Interface for SageMaker Studio operations. Must be extended to include a method for retrieving domain tags.
Signature: ListTags(ctx context.Context, params *sagemaker.ListTagsInput, optFns ...func(*sagemaker.Options)) (*sagemaker.ListTagsOutput, error)

Type: Function
Name: listAccessAnalyzers
Location: aws/resources/access_analyzer.go
Signature: listAccessAnalyzers(ctx context.Context, client AccessAnalyzerAPI, scope resource.Scope, cfg config.ResourceType) ([]*string, error)
Description: Lists access analyzers, applying tag-based filters. Tags are available directly on the AnalyzerSummary response and must be passed to config.ResourceValue when calling cfg.ShouldInclude.

Type: Function
Name: listACMCertificates
Location: aws/resources/acm.go
Signature: listACMCertificates(ctx context.Context, client ACMAPI, scope resource.Scope, cfg config.ResourceType) ([]*string, error)
Description: Lists ACM certificates, applying tag-based filters. Must call ListTagsForCertificate per certificate and pass the resulting tag map to config.ResourceValue when calling cfg.ShouldInclude.

Type: Function
Name: listACMPCA
Location: aws/resources/acmpca.go
Signature: listACMPCA(ctx context.Context, client ACMPCAAPI, scope resource.Scope, cfg config.ResourceType) ([]*string, error)
Description: Lists ACM PCA certificate authorities, applying tag-based filters. Must call ListTags per CA and pass the resulting tag map to config.ResourceValue when calling cfg.ShouldInclude.

Type: Function
Name: listAppRunnerServices
Location: aws/resources/apprunner_service.go
Signature: listAppRunnerServices(ctx context.Context, client AppRunnerServiceAPI, scope resource.Scope, cfg config.ResourceType) ([]*string, error)
Description: Lists App Runner services, applying tag-based filters. Must call ListTagsForResource per service ARN and pass the resulting tag map to config.ResourceValue when calling cfg.ShouldInclude.

Type: Function
Name: listCloudfrontDistributions
Location: aws/resources/cloudfront_distribution.go
Signature: listCloudfrontDistributions(ctx context.Context, client CloudfrontDistributionAPI, scope resource.Scope, cfg config.ResourceType) ([]*string, error)
Description: Lists CloudFront distributions, applying tag-based filters. Must call ListTagsForResource per distribution ARN and pass the resulting tag map to config.ResourceValue when calling cfg.ShouldInclude.

Type: Function
Name: listConfigServiceRules
Location: aws/resources/config_service.go
Signature: listConfigServiceRules(ctx context.Context, client ConfigServiceRuleAPI, scope resource.Scope, cfg config.ResourceType) ([]*string, error)
Description: Lists Config Service rules, applying tag-based filters. Must call ListTagsForResource using ConfigRuleArn and pass the resulting tag map to config.ResourceValue when calling cfg.ShouldInclude.

Type: Function
Name: listDataPipelines
Location: aws/resources/data_pipeline.go
Signature: listDataPipelines(ctx context.Context, client DataPipelineAPI, scope resource.Scope, cfg config.ResourceType) ([]*string, error)
Description: Lists Data Pipelines, applying tag-based filters. Tags are available directly on the PipelineDescription.Tags field and must be converted to a map[string]string and passed to config.ResourceValue when calling cfg.ShouldInclude.

Type: Function
Name: listDataSyncLocations
Location: aws/resources/datasync_location.go
Signature: listDataSyncLocations(ctx context.Context, client DataSyncLocationAPI, scope resource.Scope, cfg config.ResourceType) ([]*string, error)
Description: Lists DataSync locations, applying tag-based filters. Must call ListTagsForResource per location ARN and pass the resulting tag map to config.ResourceValue when calling cfg.ShouldInclude.

Type: Function
Name: listDataSyncTasks
Location: aws/resources/datasync_task.go
Signature: listDataSyncTasks(ctx context.Context, client DataSyncTaskAPI, scope resource.Scope, cfg config.ResourceType) ([]*string, error)
Description: Lists DataSync tasks, applying tag-based filters. Must call ListTagsForResource per task ARN and pass the resulting tag map to config.ResourceValue when calling cfg.ShouldInclude.

Type: Function
Name: listEBApplications
Location: aws/resources/elastic_beanstalk.go
Signature: listEBApplications(ctx context.Context, client EBApplicationsAPI, scope resource.Scope, cfg config.ResourceType) ([]*string, error)
Description: Lists Elastic Beanstalk applications, applying tag-based filters. Must call ListTagsForResource per application using ApplicationArn and pass the resulting tag map to config.ResourceValue when calling cfg.ShouldInclude.

Type: Function
Name: listGuardDutyDetectors
Location: aws/resources/guardduty.go
Signature: listGuardDutyDetectors(ctx context.Context, client GuardDutyAPI, scope resource.Scope, cfg config.ResourceType) ([]*string, error)
Description: Lists GuardDuty detectors, applying tag-based filters. Tags are available in the GetDetector response as Tags map[string]string and must be passed to config.ResourceValue when calling cfg.ShouldInclude.

Type: Function
Name: listSageMakerNotebookInstances
Location: aws/resources/sagemaker_notebook_instance.go
Signature: listSageMakerNotebookInstances(ctx context.Context, client SageMakerNotebookInstancesAPI, scope resource.Scope, cfg config.ResourceType) ([]*string, error)
Description: Lists SageMaker notebook instances, applying tag-based filters. Must call ListTags using NotebookInstanceArn and pass the resulting tag map to config.ResourceValue when calling cfg.ShouldInclude.

Type: Function
Name: listSageMakerDomains
Location: aws/resources/sagemaker_studio.go
Signature: listSageMakerDomains(ctx context.Context, client SageMakerStudioAPI, scope resource.Scope, cfg config.ResourceType) ([]*string, error)
Description: Lists SageMaker Studio domains, applying tag-based filters. Must call ListTags using DomainArn and pass the resulting tag map to config.ResourceValue when calling cfg.ShouldInclude.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.