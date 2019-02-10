# Artifact Type Requirements

Adding new artifacts to an OCI registry involves two distinct requirements.

- [Requirements of the new artifact type](#New-Artifact-Requirements)
- [Requirements of registries to host new artifact types](#Registry-Requirements-of-New-Artifact-Types)

## New Artifact Requirements

## Registry Requirements of New Artifact Types

To provide great customer experiences, leveraging the infrastructure implemented to support container images, registry providers have the following requirements:

- Deterministically identify the artifact type
- Minimal engineering impact to support new artifact types
- Fit within the existing persistance capabilities of [docker/distribution](https://github.com/docker/distribution) and [oci distribution](https://github.com/opencontainers/distribution-spec)
- Have well known short names to display the artifact type

### Registry Impact
Registries are currently implemented to support container images. The presumption is all objects in a registry represent one of three things:

- [**Index**](https://github.com/opencontainers/image-spec/blob/master/image-index.md) - A collection of images (manifests), differentiated by architecture and platform
- [**Manifest**](https://github.com/opencontainers/image-spec/blob/master/manifest.md) - A single image, targeting a specific architecture and platform. 
- [**Layer**](https://github.com/opencontainers/image-spec/blob/master/manifest.md#image-manifest-property-descriptions) - Represented by an [OCI descriptor](https://github.com/opencontainers/image-spec/blob/master/descriptor.md), a layer represents a persisted blob that can be pulled and cached on a client. 

The primary job of a registry is to securely store and serve images. By extending a registry to support additional artifacts a few assumptions will need to be adjusted.

### Registry Clients
Registries already support multiple clients, including:

- **docker clients**, pushing and pulling images

  Interact with a registry through `docker push/pull`
- **Registry Tools**, such as vulnerability scanners

  Query and scan images through cloud implementations of [the catalog api](https://docs.docker.com/registry/spec/api/#catalog), and `docker pull`
- **CI/CD** solutions, such as [Codefresh](https://codefresh.io), [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/), [Jenkins](https://jenkins.io/), [CircleCI](https://circleci.com/) and others that utilize cloud specific versions of [docker webhooks](https://docs.docker.com/docker-hub/webhooks/) ([acr](https://docs.microsoft.com/en-us/azure/container-registry/container-registry-webhook), [ecr](https://docs.microsoft.com/en-us/azure/container-registry/container-registry-webhook), [gcr](https://cloud.google.com/container-registry/docs/configuring-notifications), [quay](https://docs.quay.io/guides/notifications.html))

Existing clients assume the objects being returned are container images. 

*For docker v2 clients, most public registries already convert OCI Index and Manifest requests to docker/distribution v2 indexes and manifests.*

---
- [**Config**](https://github.com/opencontainers/image-spec/blob/master/manifest.md#image-manifest-property-descriptions) - An [OCI descriptor](https://github.com/opencontainers/image-spec/blob/master/descriptor.md) that provides details of the manifest, such as the specific architecture & platform. 
