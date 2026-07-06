Implement a recovery worker module to handle cloud storage synchronization failures for the OSV vulnerability database. Create distinct handlers for each failure scenario and a shared utility function for publishing failure messages to a recovery queue.

*   Implement `handle_gcs_retry` in `gcp/workers/recoverer/recoverer.py`:
    *   Deserialize the message data as a vulnerability protobuf.
    *   Log an ERROR with 'failed to decode protobuf' and return True if deserialization fails.
    *   Check if the GCS blob at `osv.gcs.VULN_PB_PATH/{vuln_id}.pb` has a `custom_time` greater than or equal to the vulnerability's modified timestamp.
    *   Log a WARNING with '{vuln_id} was modified before message was processed' and return True if the blob is newer.
    *   Write the protobuf bytes to the GCS path and set `custom_time` to the vulnerability's modified datetime if the blob is not newer. Always return True.

*   Implement `handle_gcs_missing` in `gcp/workers/recoverer/recoverer.py`:
    *   Read the 'id' attribute from `message.attributes`.
    *   Fetch the corresponding Bug entity from the datastore and re-put it to regenerate the GCS record.
    *   Ensure the GCS blob contains the same vulnerability data with a different generation number. Return True on success.

*   Implement `handle_gcs_gen_mismatch` in `gcp/workers/recoverer/recoverer.py`:
    *   Read 'id' and 'field' from `message.attributes`.
    *   For `field='aliases'`, query the AliasGroup, set `vuln.aliases` to the sorted list of the group's bug_ids excluding the vuln_id, and set `vuln.modified` to the group's last_modified. Use an empty list and current UTC time if no group exists.
    *   For `field='upstream'`, query the UpstreamGroup, set `vuln.upstream` to the group's upstream_ids, and set `vuln.modified` to the group's last_modified. Use an empty list and current UTC time if no group exists.
    *   Write the updated vulnerability to GCS and return True.

*   Implement `handle_generic` in `gcp/workers/recoverer/recoverer.py`:
    *   Read the 'type' attribute from `message.attributes`.
    *   Log an ERROR with '`{type}` task could not be processed' and return True.

*   Implement `publish_failure` in `osv/pubsub.py`:
    *   Use `pubsub_v1.PublisherClient` to publish the provided data bytes and attributes to the 'failed-tasks' Pub/Sub topic.
    *   Obtain the GCP project using `osv.utils.get_google_cloud_project()`.
    *   Log 'GOOGLE_CLOUD_PROJECT not set, cannot send retry message' and raise a `RuntimeError` if the project is not configured.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.