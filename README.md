# OpenCloud Helm Chart

This Helm chart deploys OpenCloud on Kubernetes.

## Introduction

OpenCloud is a cloud collaboration platform that provides file sync and share, document collaboration, and more. This Helm chart deploys OpenCloud with Keycloak for authentication, MinIO for object storage, and Collabora for document editing.

## Prerequisites

- Kubernetes 1.19+
- Helm 3.2.0+
- PV provisioner support in the underlying infrastructure (if persistence is enabled)
- External ingress controller (e.g., Cilium Gateway API) for routing traffic to the services

## Installing the Chart

To install the chart with the release name `my-opencloud`:

```bash
helm install opencloud .
```

## Architecture

This Helm chart deploys the following components:

1. **OpenCloud** - Main application (fork of ownCloud Infinite Scale)
2. **Keycloak** - Authentication provider with OpenID Connect
3. **PostgreSQL** - Database for Keycloak
4. **MinIO** - S3-compatible object storage
5. **Collabora** - Online document editor (CODE - Collabora Online Development Edition)
6. **Collaboration Service** - WOPI server that connects OpenCloud with Collabora

All services are deployed with `ClusterIP` type, which means they are only accessible within the Kubernetes cluster. You need to configure your own ingress controller (e.g., Cilium Gateway API) to expose the services externally.

## Configuration

The following table lists the configurable parameters of the OpenCloud chart and their default values.

### Global Settings

| Parameter | Description | Default |
| --------- | ----------- | ------- |
| `namespace` | Namespace to install the chart into | `opencloud` |
| `global.domain.opencloud` | Domain for OpenCloud | `cloud.opencloud.test` |
| `global.domain.keycloak` | Domain for Keycloak | `keycloak.opencloud.test` |
| `global.domain.minio` | Domain for MinIO | `minio.opencloud.test` |
| `global.domain.collabora` | Domain for Collabora | `collabora.opencloud.test` |
| `global.domain.onlyoffice` | Domain for OnlyOffice | `onlyoffice.opencloud.test` |
| `global.domain.companion` | Domain for Companion | `companion.opencloud.test` |
| `global.tls.enabled` | Enable TLS | `true` |
| `global.tls.selfSigned` | Use self-signed certificates | `true` |
| `global.tls.acmeEmail` | ACME email for Let's Encrypt | `example@example.org` |
| `global.tls.acmeCAServer` | ACME CA server | `https://acme-v02.api.letsencrypt.org/directory` |
| `global.storage.storageClass` | Storage class for persistent volumes | `""` |

### Image Settings

| Parameter | Description | Default |
| --------- | ----------- | ------- |
| `image.repository` | OpenCloud image repository | `opencloudeu/opencloud-rolling` |
| `image.tag` | OpenCloud image tag | `latest` |
| `image.pullPolicy` | Image pull policy | `IfNotPresent` |
| `image.pullSecrets` | Image pull secrets | `[]` |

### OpenCloud Settings

| Parameter | Description | Default |
| --------- | ----------- | ------- |
| `opencloud.enabled` | Enable OpenCloud | `true` |
| `opencloud.replicas` | Number of replicas | `1` |
| `opencloud.logLevel` | Log level | `info` |
| `opencloud.logColor` | Enable log color | `false` |
| `opencloud.logPretty` | Enable pretty logging | `false` |
| `opencloud.insecure` | Insecure mode (for self-signed certificates) | `true` |
| `opencloud.enableBasicAuth` | Enable basic auth | `false` |
| `opencloud.adminPassword` | Admin password | `admin` |
| `opencloud.createDemoUsers` | Create demo users | `false` |
| `opencloud.resources` | CPU/Memory resource requests/limits | `{}` |
| `opencloud.persistence.enabled` | Enable persistence | `true` |
| `opencloud.persistence.size` | Size of the persistent volume | `10Gi` |
| `opencloud.persistence.storageClass` | Storage class | `""` |
| `opencloud.persistence.accessMode` | Access mode | `ReadWriteOnce` |
| `opencloud.storage.driver` | Storage driver (posix, decomposed, decomposeds3) | `decomposeds3` |
| `opencloud.storage.systemDriver` | System storage driver (posix, decomposed) | `decomposed` |
| `opencloud.storage.s3.endpoint` | S3 endpoint (defaults to MinIO service if empty) | `""` |
| `opencloud.storage.s3.region` | S3 region | `default` |
| `opencloud.storage.s3.accessKey` | S3 access key (defaults to MinIO root user if empty) | `""` |
| `opencloud.storage.s3.secretKey` | S3 secret key (defaults to MinIO root password if empty) | `""` |
| `opencloud.storage.s3.bucket` | S3 bucket (defaults to MinIO bucket name if empty) | `""` |
| `opencloud.storage.s3.createBucket` | Create bucket if it doesn't exist | `true` |

### Keycloak Settings

| Parameter | Description | Default |
| --------- | ----------- | ------- |
| `keycloak.enabled` | Enable Keycloak | `true` |
| `keycloak.replicas` | Number of replicas | `1` |
| `keycloak.adminUser` | Admin user | `admin` |
| `keycloak.adminPassword` | Admin password | `admin` |
| `keycloak.resources` | CPU/Memory resource requests/limits | `{}` |
| `keycloak.realm` | Realm name | `openCloud` |
| `keycloak.persistence.enabled` | Enable persistence | `true` |
| `keycloak.persistence.size` | Size of the persistent volume | `1Gi` |
| `keycloak.persistence.storageClass` | Storage class | `""` |
| `keycloak.persistence.accessMode` | Access mode | `ReadWriteOnce` |

### PostgreSQL Settings

| Parameter | Description | Default |
| --------- | ----------- | ------- |
| `postgres.enabled` | Enable PostgreSQL | `true` |
| `postgres.database` | Database name | `keycloak` |
| `postgres.user` | Database user | `keycloak` |
| `postgres.password` | Database password | `keycloak` |
| `postgres.resources` | CPU/Memory resource requests/limits | `{}` |
| `postgres.persistence.enabled` | Enable persistence | `true` |
| `postgres.persistence.size` | Size of the persistent volume | `1Gi` |
| `postgres.persistence.storageClass` | Storage class | `""` |
| `postgres.persistence.accessMode` | Access mode | `ReadWriteOnce` |

### MinIO Settings

| Parameter | Description | Default |
| --------- | ----------- | ------- |
| `minio.enabled` | Enable MinIO | `true` |
| `minio.rootUser` | Root user | `opencloud` |
| `minio.rootPassword` | Root password | `opencloud-secret-key` |
| `minio.bucketName` | Bucket name | `opencloud-bucket` |
| `minio.resources` | CPU/Memory resource requests/limits | `{}` |
| `minio.persistence.enabled` | Enable persistence | `true` |
| `minio.persistence.size` | Size of the persistent volume | `10Gi` |
| `minio.persistence.storageClass` | Storage class | `""` |
| `minio.persistence.accessMode` | Access mode | `ReadWriteOnce` |

### Collabora Settings

| Parameter | Description | Default |
| --------- | ----------- | ------- |
| `collabora.enabled` | Enable Collabora | `true` |
| `collabora.repository` | Collabora image repository | `collabora/code` |
| `collabora.tag` | Collabora image tag | `24.04.13.2.1` |
| `collabora.pullPolicy` | Image pull policy | `IfNotPresent` |
| `collabora.adminUser` | Admin user | `admin` |
| `collabora.adminPassword` | Admin password | `admin` |
| `collabora.ssl.enabled` | Enable SSL | `true` |
| `collabora.ssl.verification` | SSL verification | `true` |
| `collabora.resources` | CPU/Memory resource requests/limits | `{}` |

### Collaboration Service Settings

| Parameter | Description | Default |
| --------- | ----------- | ------- |
| `collaboration.enabled` | Enable collaboration service | `true` |
| `collaboration.wopiDomain` | WOPI server domain | `collaboration.opencloud.test` |
| `collaboration.resources` | CPU/Memory resource requests/limits | `{}` |


## Cilium Gateway API Configuration

This chart includes Cilium HTTPRoute resources that can be used to expose the OpenCloud, Keycloak, and MinIO services externally. The HTTPRoutes are configured to route traffic to the respective services.

### Cilium HTTPRoute Settings

| Parameter | Description | Default |
| --------- | ----------- | ------- |
| `cilium.httproute.enabled` | Enable Cilium HTTPRoutes | `true` |
| `cilium.httproute.gateway.name` | Gateway name | `cilium-gateway` |
| `cilium.httproute.gateway.namespace` | Gateway namespace | `""` (defaults to Release.Namespace) |

The following HTTPRoutes are created when `cilium.httproute.enabled` is set to `true`:

1. **OpenCloud HTTPRoute**:
   - Hostname: `global.domain.opencloud`
   - Service: `{{ release-name }}-opencloud`
   - Port: 9200

2. **Keycloak HTTPRoute** (when `keycloak.enabled` is `true`):
   - Hostname: `global.domain.keycloak`
   - Service: `{{ release-name }}-keycloak`
   - Port: 8080

3. **MinIO HTTPRoute** (when `minio.enabled` is `true`):
   - Hostname: `global.domain.minio`
   - Service: `{{ release-name }}-minio`
   - Port: 9001

4. **Collabora HTTPRoute** (when `collabora.enabled` is `true`):
   - Hostname: `global.domain.collabora`
   - Service: `{{ release-name }}-collabora`
   - Port: 9980

5. **Collaboration (WOPI) HTTPRoute** (when `collaboration.enabled` is `true`):
   - Hostname: `collaboration.wopiDomain`
   - Service: `{{ release-name }}-collaboration`
   - Port: 9300

All HTTPRoutes are configured to use the same Gateway specified by `cilium.httproute.gateway.name` and `cilium.httproute.gateway.namespace`.

