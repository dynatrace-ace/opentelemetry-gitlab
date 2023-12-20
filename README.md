# Gitlab OpenTelemetry Exporters

## How to monitor Gitlab with Dynatrace

Follow the below to enable the observability of your GitLab instance using Dynatrace and its support for OpenTelemetry.

The exporters allow you to:
- Generate traces for your CI/CD executions, including meta data
- Access your GitLab logs in context within Dynatrace 
- Visualise the status of your pipelines and GitLab instance through metrics on dashboards


# OpenTelemetry Exporter

## Deploy an OpenTelemetry Collector and configure it for export to Dynatrace

Check out https://docs.dynatrace.com/docs/shortlink/otel-collector for more information

## Adding stage to gitlab-ci.yml

1. Add the following stage definition to your `gitlab-ci.yml` file.
    ```yml
    otel-exporter:
    rules:
    - when: always
    stage: otel-exporter
    inherit:
    variables: true
    variables:
    CI_PARENT_PIPELINE: $CI_PIPELINE_ID
    trigger:
    include:
        - local: otel-exporter.yml
    ```

1. Also add it as the last `stages` definition in that file:
    ```yml
    stages:
    - ...
    - otel-exporter
    ```

1. Ensure that the `otel-exporter.yml` file is present in the repo

1. Set the following variables, either within the `gitlab-ci.yml` file, or on the project or group level


| Variables | Description | Optional | Values | Default |
| ---       |         --- |       ---| ---    |   ----   |
| `OTEL_EXPORTER_OTEL_ENDPOINT` | OTEL endpoint including port | True | String | |
| `GLAB_TOKEN` | MASKED - Token to access gitlab API | False | String | None |
| `GLAB_EXPORT_LOGS` | Export job logs to OpenTelemetry Collector | True | Boolean | True |
| `GLAB_ENDPOINT` | Gitlab API endpoint | True | String | "https://gitlab.com" |
| `GLAB_CONVERT_TO_TIMESTAMP` | converts datetime to timestamp | True | Boolean | False |

<!-- # OpenTelemetry Metrics Exporter
| Variables | Description | Optional | Values | Default |
| ---       |         --- |       ---| ---    |   ----   |
| `OTEL_EXPORTER_OTEL_ENDPOINT` | OTEL endpoint including port | True | String | |
| `GLAB_ENDPOINT` | Gitlab API endpoint | True | String | "https://gitlab.com" |
| `GLAB_TOKEN` | MASKED - Token to access gitlab API | False | String | None |
| `GLAB_PROJECT_OWNERSHIP` | Project ownership | False | String | True |
| `GLAB_PROJECT_VISIBILITY` | Project visibility | False | String | private |
| `GLAB_DORA_METRICS` | Export DORA metrics, requires Gitlab ULTIMATE | True | Bool | False |
| `GLAB_EXPORT_PATHS` | Project paths aka namespace full_path to obtain data from | False | List* | None if running as standalone or CI_PROJECT_ROOT_NAMESPACE if running as pipeline schedule|
| `GLAB_EXPORT_PROJECTS_REGEX` | Regex to match project names against “.*” for all | False | Boolean | None |
| `GLAB_CONVERT_TO_TIMESTAMP` | converts datetime to timestamp | True | Boolean | False |
| `GLAB_EXPORT_LAST_MINUTES` | The amount past minutes to export data from | True | Integer | 60 |
| `GLAB_ATTRIBUTES_DROP` | Attributes to drop from logs and spans events | True | List* | None |
| `GLAB_DIMENSION_METRICS` | Extra dimensional metric attributes to add to each metric | True | List* | NONE Note the following attributes will always be set as dimensions regardless of this setting: status,stage,name |
| `GLAB_STANDALONE` | Set to True if not running as gitlab pipeline schedule | True | Boolean | False |
| `GLAB_ENVS_DROP` | Extra system environment variables to drop from span attributes | True | List* | Note the following environment variables will always be dropped regardless of this setting: GITLAB_FEATURES,CI_SERVER_TLS_CA_FILE,CI_RUNNER_TAGS,CI_JOB_JWT,CI_JOB_JWT_V1,CI_JOB_JWT_V2,GLAB_TOKEN,GIT_ASKPASS,CI_COMMIT_BEFORE_SHA,CI_BUILD_TOKEN,CI_DEPENDENCY_PROXY_PASSWORD,CI_RUNNER_SHORT_TOKEN,CI_BUILD_BEFORE_SHA,CI_BEFORE_SHA,OTEL_EXPORTER_OTEL_ENDPOINT,GLAB_DIMENSION_METRICS |
*comma separated -->


