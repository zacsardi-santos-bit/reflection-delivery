## Description

Prowler currently lacks security checks for AWS VPC architecture best practices related to high availability and network segmentation. There are no checks to verify that:

1. VPCs are deployed across multiple AWS regions for disaster recovery
2. Subnets within a VPC are spread across multiple availability zones for fault tolerance
3. VPCs properly separate public and private subnets for network security

Additionally, the VPC service does not collect subnet information, which is needed to perform these checks.

## Expected Behavior

- The `vpc_different_regions` check should verify that VPCs exist in more than one AWS region and report PASS if they do, or FAIL if all VPCs are in a single region
- The `vpc_subnet_different_az` check should verify that each VPC has subnets in at least two different availability zones and report accordingly
- The `vpc_subnet_separate_private_public` check should verify that each VPC has both public and private subnets (based on route table configuration to internet gateway) and report accordingly
- The VPC service should retrieve and store subnet information including subnet ID, VPC ID, CIDR block, availability zone, and whether the subnet is public or private

## Current Behavior

- No check exists for verifying VPCs are distributed across multiple regions
- No check exists for verifying subnets are distributed across availability zones
- No check exists for verifying separation of public and private subnets
- The VPC service does not collect subnet information needed for these checks
