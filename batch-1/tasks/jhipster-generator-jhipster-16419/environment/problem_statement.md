## Description

When generating Helm-based Kubernetes deployment configurations for JHipster applications, the generated chart metadata files are missing the database and infrastructure dependency declarations. Without these declarations, Helm does not know which external charts to fetch when installing the application, leaving developers to manually add these entries after generation.

Additionally, some of the Helm chart repository URLs referenced in the existing dependency files are outdated. Several repositories previously pointed to locations that are now deprecated or no longer actively maintained, causing Helm dependency installation to fail or fetch outdated chart versions. Specifically, the PostgreSQL and MariaDB charts should be fetched from the Bitnami chart repository, the Prometheus chart (now published under a different name) should be fetched from the Prometheus Community repository, and Grafana should be fetched from the Grafana Helm charts repository.

## Expected Behavior

- Generated application chart metadata files should include a dependency section listing the appropriate database chart (MySQL, PostgreSQL, MariaDB, or MongoDB) with the correct name, version, and repository URL.
- Generated central-services chart metadata files should include a dependency section for Kafka and/or monitoring tools (Prometheus and Grafana) when those features are enabled, with the correct chart names and repository URLs.
- Even when an application has no database or messaging dependencies, the dependency section key should still be present in the generated file (though empty).
- Existing dependency specification files must have their repository URLs corrected to point to the current maintained chart repositories, and the Prometheus chart must be referenced by its updated name.

## Why This Matters

Developers using the Helm generator expect generated files to be immediately usable — running Helm dependency installation after generation should just work without requiring manual edits to fix missing declarations or broken repository references.
