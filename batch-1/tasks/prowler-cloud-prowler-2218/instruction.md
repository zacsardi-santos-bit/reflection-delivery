Implement new security checks for AWS VPC architecture best practices in Prowler. Ensure VPCs are distributed across multiple regions, subnets span multiple availability zones, and there is proper separation of public and private subnets. Extend the VPC service to collect necessary subnet information.

*   Update the VPC service:
    *   Implement the `__describe_vpc_subnets__` method in `prowler/providers/aws/services/vpc/vpc_service.py` to retrieve subnet information, determine if each is public or private, and associate them with their parent VPC objects.
    *   Ensure the `VpcSubnet` model in `prowler/providers/aws/services/vpc/vpc_service.py` includes properties: `id`, `default`, `vpc_id`, `cidr_block`, `availability_zone`, `public`, `region`, and `tags`.
    *   Identify a subnet as public if its route table contains a route with a `GatewayId` containing 'igw'.
    *   Associate subnets not explicitly linked to a route table with the main route table.
    *   Ensure the VPCs model has a `subnets` property containing a list of `VpcSubnet` objects.

*   Implement the `vpc_different_regions` check:
    *   Create the `vpc_different_regions` class in `prowler/providers/aws/services/vpc/vpc_different_regions/vpc_different_regions.py`.
    *   Implement the `execute` method to verify VPCs exist in more than one AWS region, skipping default VPCs.
    *   Return a PASS status with the message 'VPCs found in more than one region.' if VPCs span multiple regions.
    *   Return a FAIL status with the message 'VPCs found only in one region {region}.' if all VPCs are in a single region.

*   Implement the `vpc_subnet_different_az` check:
    *   Create the `vpc_subnet_different_az` class in `prowler/providers/aws/services/vpc/vpc_subnet_different_az/vpc_subnet_different_az.py`.
    *   Implement the `execute` method to verify each VPC has subnets in more than one availability zone.
    *   Return a PASS status with the message 'VPC {vpc_id} has subnets in more than one availability zone.' if applicable.
    *   Return a FAIL status with the message 'VPC {vpc_id} has only subnets in {availability_zone}.' if all subnets are in the same availability zone.
    *   Return a FAIL status with the message 'VPC {vpc_id} has no subnets.' if a VPC has no subnets.

*   Implement the `vpc_subnet_separate_private_public` check:
    *   Create the `vpc_subnet_separate_private_public` class in `prowler/providers/aws/services/vpc/vpc_subnet_separate_private_public/vpc_subnet_separate_private_public.py`.
    *   Implement the `execute` method to verify each VPC has both public and private subnets.
    *   Return a PASS status with the message 'VPC {vpc_id} has private and public subnets.' if applicable.
    *   Return a FAIL status with the message 'VPC {vpc_id} has only public subnets.' if a VPC has only public subnets.
    *   Return a FAIL status with the message 'VPC {vpc_id} has only private subnets.' if a VPC has only private subnets.
    *   Return a FAIL status with the message 'VPC {vpc_id} has no subnets.' if a VPC has no subnets.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.