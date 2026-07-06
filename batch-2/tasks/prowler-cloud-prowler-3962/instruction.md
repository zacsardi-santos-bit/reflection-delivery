Implement a new security check to detect when a security group allows all ports to be accessed from the internet and update the existing check to correctly flag any single port open to the internet. Ensure both checks respect the "ignore unused services" setting.

*   Create a new class `ec2_securitygroup_allow_ingress_from_internet_to_all_ports` in `prowler/providers/aws/services/ec2/ec2_securitygroup_allow_ingress_from_internet_to_all_ports/ec2_securitygroup_allow_ingress_from_internet_to_all_ports.py`.
    *   Use module-level `ec2_client` and `vpc_client`.
    *   Implement the `execute(self) -> list` method:
        *   Return a FAIL result with `status_extended`: 'Security group {name} ({id}) has all ports open to the Internet.' when all ports are exposed to the internet.
        *   Return a PASS result with `status_extended`: 'Security group {name} ({id}) does not have all ports open to the Internet.' when not all ports are open.
        *   Set `resource_arn`, `resource_details`, and `resource_tags` appropriately.
        *   Skip security groups in unused VPCs when `scan_unused_services` is False.

*   Update the existing class `ec2_securitygroup_allow_ingress_from_internet_to_any_port` in `prowler/providers/aws/services/ec2/ec2_securitygroup_allow_ingress_from_internet_to_any_port/ec2_securitygroup_allow_ingress_from_internet_to_any_port.py`.
    *   Modify the `execute(self) -> list` method:
        *   Change FAIL `status_extended` to: 'Security group {name} ({id}) has at least one port open to the Internet.'
        *   Change PASS `status_extended` to: 'Security group {name} ({id}) does not have any port open to the Internet.'
        *   Ensure it flags FAIL for any single port open to the internet.

*   Update the `check_security_group` function in `prowler/providers/aws/services/ec2/lib/security_groups.py`.
    *   Support `any_address` as a keyword argument.
    *   Default `ports` to allow omission when using `any_address`.
    *   Return True if protocol is '-1' and `any_address` is True, with CIDR 0.0.0.0/0 or ::/0.
    *   Return True when `ports=None` if any port is accessible from a public address.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.