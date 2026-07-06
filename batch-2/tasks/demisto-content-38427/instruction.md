Update the Absolute security integration to support the provider's third-generation API. Implement changes to endpoint paths, payload field names, and response structures for device management operations. Ensure all commands align with the new API specifications.

*   Update the `ClientV3` class:
    *   Implement `prepare_request(self, method: str, url_suffix: str, query_string: str, body: dict = {}) -> str`:
        *   Call `jwt.encode` with `{}` and the client secret key when `body` is empty.
        *   Call `jwt.encode` with `{'data': body}` and the secret key when `body` is non-empty.
    *   Implement `send_request_to_api(self, method: str, url_suffix: str, query_string: str, ok_codes: tuple, payload: dict = {}, resp_type: str = "json") -> dict or list`:
        *   Replace `fetch_events_request` with this method.
        *   Pass all core arguments positionally in `fetch_events_between_dates`.
        *   Pass `ok_codes` as a keyword argument in list and delete freeze message paths.
    *   Implement `add_pagination(self, next_page: str, page_size: int) -> str`:
        *   Return `&nextPage={next_page}&pageSize={page_size}` if `next_page` is non-empty.
        *   Return `&pageSize={page_size}` if `next_page` is empty.

*   Implement the `remove_device_freeze_request_command(args: dict, client: ClientV3) -> CommandResults` function:
    *   Return a `CommandResults` object with `readable_output` as "Successfully removed freeze request for devices ids: 1." when `device_ids` is '1'.

*   Implement the `get_custom_device_field_list_command(client: ClientV3, args: dict) -> CommandResults` function:
    *   Use `args['device_id']` for `DeviceUID` in outputs.

*   Implement the `prepare_payload_to_freeze_request(args: dict) -> dict` function:
    *   Use `scheduledFreezeDateTimeUtc` for the scheduled freeze date.
    *   Use `requestTitle` for the request name field.
    *   Exclude `notificationEmails` from the payload.
    *   Accept `device_freeze_type='OffLine'` and pass it as-is.

*   Implement the `get_device_freeze_request_command(args: dict, client: ClientV3) -> CommandResults` function:
    *   Exclude `AccountUid` and `RequesterUid` from output.
    *   Default `Configuration` to `{}` and `Statuses` to `[]` if absent/empty.

*   Implement the `device_unenroll_command(args: dict, client: ClientV3) -> CommandResults` function:
    *   Use a three-step API flow:
        1.   POST to `/v3/actions/requests/unenroll` to obtain `requestUid`.
        2.   GET `/v3/actions/requests/unenroll/{requestUid}` for summary data.
        3.   Retrieve a list of device action records.
    *   Output a single dict with specified keys and structure.

*   Implement the `list_device_freeze_message_command(client: ClientV3, args: dict) -> CommandResults` function:
    *   Call `send_request_to_api` with 3 positional args when no `message_id` is provided.
    *   Ensure output list dicts have keys in specified order.

*   Implement the `delete_device_freeze_message_command(client: ClientV3, args: dict) -> CommandResults` function:
    *   Call `send_request_to_api` with 3 positional args for deletion.

*   Implement the `update_device_freeze_message_command(client: ClientV3, args: dict) -> CommandResults` function:
    *   Call `api_request_absolute` with positional args for update.

*   Implement the `create_device_freeze_message_command(client: ClientV3, args: dict) -> CommandResults` function:
    *   Call `api_request_absolute` with positional args for creation.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.