Implement a quarantine feature for the package index admin interface to allow administrators to place suspicious projects in quarantine, making them unavailable for installation and preventing modifications by owners. Ensure this action is reversible, allowing projects to be restored if investigations find no issues.

*   Update the `LifecycleStatus` enum in `warehouse/packaging/models.py`:
    *   Add `QuarantineEnter` with value `'quarantine-enter'`.
    *   Add `QuarantineExit` with value `'quarantine-exit'`.

*   Implement `quarantine_project` in `warehouse/utils/project.py`:
    *   Accept parameters: `project`, `request`, and optional `flash` (default `True`).
    *   Set `project.lifecycle_status` to `LifecycleStatus.QuarantineEnter`.
    *   Set `project.lifecycle_status_note` to `'Quarantined by {username}.'`.
    *   Update `project.lifecycle_status_changed` to a non-null value.
    *   If `flash=True`, call `request.session.flash` with message `'Project {project.name} quarantined.\nPlease update related Help Scout conversations.'` and `queue='success'`.

*   Implement `clear_project_quarantine` in `warehouse/utils/project.py`:
    *   Accept parameters: `project`, `request`, and optional `flash` (default `True`).
    *   Set `project.lifecycle_status` to `LifecycleStatus.QuarantineExit`.
    *   If `flash=True`, call `request.session.flash` with message `'Project {project.name} quarantine cleared.\nPlease update related Help Scout conversations.'` and `queue='success'`.

*   Create admin views in `warehouse/admin/views/malware_reports.py`:
    *   `malware_reports_project_verdict_quarantine`:
        *   Accept `project` and `request`.
        *   Quarantine the project using `quarantine_project`.
        *   Flash success message and return `HTTPSeeOther` redirect.
    *   `verdict_quarantine_project`:
        *   Accept `request`.
        *   Look up observation by `request.matchdict['observation_id']`.
        *   Quarantine the related project using `quarantine_project`.
        *   Flash success message and return `HTTPSeeOther` redirect.

*   Implement `remove_from_quarantine` in `warehouse/admin/views/projects.py`:
    *   Accept `project` and `request`.
    *   Clear quarantine status using `clear_project_quarantine`.
    *   Flash success message.

*   Register admin routes in `warehouse/admin/routes.py`:
    *   `admin.project.remove_from_quarantine` at `/admin/projects/{project_name}/remove_from_quarantine/`.
    *   `admin.malware_reports.project.verdict_quarantine` at `/admin/projects/{project_name}/malware_reports/quarantine/`.
    *   `admin.malware_reports.detail.verdict_quarantine` at `/admin/malware_reports/{observation_id}/quarantine/`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.