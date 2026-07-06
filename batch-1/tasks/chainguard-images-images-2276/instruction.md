Create a new directory structure for the Grafana Agent Operator container image, ensuring it adheres to the repository's conventions for hardened images. Integrate this image into the existing build and deployment pipeline, ensuring all configurations and documentation are correctly set up.

*   Create the directory `images/grafana-agent-operator/` with the following structure:
    *   `main.tf`: Root Terraform configuration.
        *   Declare the OCI provider.
        *   Accept a `target_repository` variable.
        *   Source `./config` and `./tests` modules.
        *   Use `tflib/publisher` module to build and publish the image with `build-dev = true`.
        *   Create `oci_tag` resources for "latest" and "latest-dev" that depend on the test module.
    *   `config/` subdirectory:
        *   `main.tf`: Terraform configuration for the APK image build.
            *   Declare the `apko` provider.
            *   Accept an `extra_packages` variable defaulting to `["grafana-agent-operator"]`.
            *   Load the `apko` config from `template.apko.yaml`.
            *   Output the JSON-encoded config.
        *   `template.apko.yaml`: APK image template.
            *   Configure a nonroot user and group (UID/GID 65532).
            *   Set `run-as` to "65532".
            *   Set the entrypoint command to `grafana-agent-operator`.
    *   `metadata.yaml`: Image metadata.
        *   Include fields: `name` (grafana-agent-operator), `image` (cgr.dev/chainguard/grafana-agent-operator), `logo` (a URL), `readme_file` (README.md), `upstream_url`, and `keywords` (a list).
    *   `README.md`: Image documentation.
        *   Include monopod section markers: `<!--monopod:start--> / <!--monopod:end-->`, `<!--overview:start--> / <!--overview:end-->`, `<!--getting:start--> / <!--getting:end-->`, and `<!--body:start--> / <!--body:end-->`.

*   Ensure the file `images/grafana-agent-operator/tests/verify_deployment.sh` has executable permissions.

*   Modify the Terraform test configuration in `images/grafana-agent-operator/tests/main.tf` to reference the image by digest via an input variable.

*   Update the root `main.tf` in the repository:
    *   Include a module block named `grafana-agent-operator` that sources `./images/grafana-agent-operator`.
    *   Pass `target_repository = "${var.target_repository}/grafana-agent-operator"`.
    *   Ensure this block is in alphabetical order relative to other module blocks.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.