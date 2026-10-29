# Data Storage Systems

Kubernetes architecture coursework for a proposed Japanese-language learning platform, completed for the Data Storage Systems course at the National University of Science and Technology MISIS.

This repository contains a system design and illustrative infrastructure manifests. It does not include application source code, published container images, or a complete runnable deployment.

## System design

The proposal combines a learning web application, a PostgreSQL database, learner assessment, a notification queue, and a language-data API. The original design also describes an API outside Kubernetes with a proposed allocation of 2 CPUs and 4 GB RAM.

| Component | Intended role | Port | Checked-in example |
| --- | --- | --- | --- |
| Learning application | Lessons, exercises, and learning resources | 80 | Deployment and ConfigMap |
| PostgreSQL database | Users, progress, lessons, and materials | 5432 | StatefulSet, ConfigMap, and Secret |
| Assessment service | Evaluate learner results | 8080 | ConfigMap only |
| Notification queue | Asynchronous learning notifications | 5672 | Deployment and ConfigMap |
| Language-data API | Additional vocabulary and language resources | 8000 | Deployment, ConfigMap, and Secret |

Ports and roles come from the design document. The `image/...:latest` names are placeholders; no images are built or distributed here.

## Repository contents

| File | Contents |
| --- | --- |
| [description.txt](description.txt) | Course assignment, translated into English |
| [project_overview.pdf](project_overview.pdf) | Original Russian-language system description and architecture diagrams |
| [project.yaml](project.yaml) | Three Deployment examples and one database StatefulSet |
| [configmaps.yaml](configmaps.yaml) | Five configuration examples, including one for the proposed assessment service |
| [secrets.yaml](secrets.yaml) | Two Secret examples with explicit replacement values |

Each YAML file contains separate documents delimited by `---`, so a YAML reader can load every resource. Workloads reference the `dss` namespace, which is not created by these files.

## Read and adapt the examples

Clone the repository and start with the assignment and PDF:

```sh
git clone https://github.com/andreystashev/data-storage-systems.git
cd data-storage-systems
```

The examples show Kubernetes resource structure and configuration references. To turn the proposal into a working deployment, supply the application implementations and real image names, create the `dss` namespace, and complete the missing infrastructure:

- Services and Ingress: the configured service DNS names currently have no corresponding Service resources.
- Persistent storage: the database StatefulSet has no volume mounts or persistent volume claims.
- Assessment workload and scheduled work: its Deployment, a Job/CronJob, and the related integration are not supplied.
- Runtime configuration: replace example URLs and Secret values, choose actual external dependencies, and configure resource requests/limits and health checks for the workloads.

`EXTERNAL_API_URL` points to the proposed `language-api-service`. The queue and language-data URLs are illustrative configuration; the repository has no application code that consumes them. A service address ending in `:5672` should use the protocol expected by the selected message broker, rather than assuming HTTP.

Database connection credentials belong to the Secret example. The learning application receives `DATABASE_URL` from `japanese-learning-db-secret`, while the database also receives its user and database name from its ConfigMap. Update the password and connection URL together. `stringData` accepts plain text and is converted by Kubernetes into Secret data; base64 encoding is not encryption. Keep real credentials out of version control.

## Validation scope

The manifests can be loaded with a multi-document YAML parser for local inspection. Syntax and matching ConfigMap/Secret references do not prove that the example images, services, or external integrations exist. No live cluster deployment, availability test, or performance measurement is included.

The PDF is preserved as the historical coursework submission. Its proposed architecture is broader than the resources currently implemented in YAML.
