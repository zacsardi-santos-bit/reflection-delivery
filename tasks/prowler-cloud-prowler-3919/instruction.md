Implement AWS Lightsail support in Prowler by creating a service module that discovers and audits Lightsail instances, managed databases, and static IP addresses. Develop four security checks to identify common misconfigurations and produce human-readable reports.

*   Implement the `Lightsail` class in `prowler/providers/aws/services/lightsail/lightsail_service.py`:
    *   Initialize with a provider object and set `service = "lightsail"`.
    *   Store resources in three dictionaries keyed by ARN: `instances`, `databases`, `static_ips`.
    *   Use AWS APIs: `GetInstances`, `GetRelationalDatabases`, `GetStaticIps`.

*   Define data models for Lightsail resources:
    *   `Instance` model with fields: name, id, tags, region, availability_zone, static_ip, public_ip, private_ip, ipv6_addresses, ip_address_type, ports, auto_snapshot.
    *   `PortRange` model with fields: range, protocol, access_from, access_type.
    *   `Database` model with fields: name, id, tags, region, availability_zone, engine, engine_version, status, master_username, public_access.
    *   `StaticIP` model with fields: name, id, region, availability_zone, ip_address, is_attached, attached_to.

*   Develop the following check classes:
    *   `lightsail_database_public` in `prowler/providers/aws/services/lightsail/lightsail_database_public/lightsail_database_public.py`:
        *   Return FAIL if database is public, PASS if not.
        *   Include resource ARN, ID, tags, and region in results.
    *   `lightsail_instance_automated_snapshots` in `prowler/providers/aws/services/lightsail/lightsail_instance_automated_snapshots/lightsail_instance_automated_snapshots.py`:
        *   Return FAIL if instance lacks automated snapshots, PASS if enabled.
        *   Include resource ARN, ID, tags, and region in results.
    *   `lightsail_instance_public` in `prowler/providers/aws/services/lightsail/lightsail_instance_public/lightsail_instance_public.py`:
        *   Return FAIL if instance is publicly exposed, listing open ports; PASS if not.
        *   Include resource ARN, ID, tags, and region in results.
    *   `lightsail_static_ip_unused` in `prowler/providers/aws/services/lightsail/lightsail_static_ip_unused/lightsail_static_ip_unused.py`:
        *   Return FAIL if static IP is unused, PASS if attached.
        *   Include resource ARN, ID, empty tags, and region in results.

*   Ensure each check class returns an empty list if no resources are found.
*   Add `BASE_LIGHTSAIL_ARN` constant in `tests/providers/aws/utils.py` with value `f"arn:aws:lightsail:{AWS_REGION_US_EAST_1}:0000000000000:"`.

*   Create necessary `__init__.py` files in each directory.
*   Include `.metadata.json` files in each check directory.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.