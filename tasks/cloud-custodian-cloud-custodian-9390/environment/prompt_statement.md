I'm working with Cloud Custodian to manage our AWS infrastructure, and I've noticed there's no support for SageMaker HyperPod clusters. We use Cloud Custodian policies to enforce tagging, audit network placements, and clean up resources across our account, but our HyperPod clusters are completely invisible to those policies.

I need a new resource type added to the SageMaker module that lets us target these clusters in policies. Specifically, I want to be able to filter clusters by tag presence or absence, filter by the subnets and security groups they're attached to, apply or remove tags through policy actions, and delete clusters that don't meet our standards. The delete action should be safe to run even if a cluster has already been removed.

This new resource type also needs to be registered properly in the resource map so it's recognized by the framework, and the metadata validation checks should account for the fact that this resource type doesn't have a CloudFormation mapping.
