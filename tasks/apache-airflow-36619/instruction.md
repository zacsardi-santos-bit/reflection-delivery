Implement the necessary changes to the AWS authentication manager to enforce real access-control policies for DAG operations using Amazon Verified Permissions. Ensure that the authorization system gracefully handles cases where no user object is available and supports a configurable AWS region.

Requirements:
*   Define the constant:
    *   CONF_REGION_NAME_KEY in `airflow/providers/amazon/aws/auth_manager/constants.py` must equal "region_name".
*   Update the AVP facade:
    *   Modify the `is_authorized` method in `AwsAuthManagerAmazonVerifiedPermissionsFacade` located in `airflow/providers/amazon/aws/auth_manager/avp/facade.py`:
        *   Replace the `entity_fetcher` parameter with `context` (type `dict | None`, default `None`).
        *   Return `False` immediately if `user` is `None`.
        *   If `context` is a non-empty dictionary, wrap it as `{"contextMap": context}` before passing it to the AVP client.
        *   Use `prune_dict` to omit `None`-valued parameters from the AVP client call.
*   Update the AwsAuthManager:
    *   Modify the `is_authorized_dag` method in `AwsAuthManager` located in `airflow/providers/amazon/aws/auth_manager/aws_auth_manager.py`:
        *   Call `avp_facade.is_authorized` with `entity_type=AvpEntities.DAG`.
        *   Use `entity_id=details.id` if `details` is provided, otherwise `None`.
        *   Pass `context` as `None` if `access_entity` is `None`.
        *   Pass `context` as `{"dag_entity": {"string": access_entity.value}}` if `access_entity` is provided.
        *   Return the boolean result from `avp_facade.is_authorized`.
    *   Ensure the following methods return the boolean result of `avp_facade.is_authorized`:
        *   `is_authorized_configuration`
        *   `is_authorized_connection`
        *   `is_authorized_dataset`
        *   `is_authorized_pool`
        *   `is_authorized_variable`
        *   `is_authorized_view`

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.