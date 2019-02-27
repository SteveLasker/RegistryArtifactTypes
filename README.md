# Using OCI Compliant Registries as Artifact Registries

[OCI Distribution is evolving to support additional artifact types](https://stevelasker.blog/2019/01/25/cloud-native-artifact-stores-evolve-from-container-registries/). 
This repo presents information on how registries and artifact authors can collaborate to have all OCI compliant registries be capable of supporting any new OCI compliant artifact. 

## Overview

The [OCI image-spec](https://github.com/opencontainers/image-spec) and [OCI distribution-spec](https://github.com/opencontainers/distribution-spec) were designed to support platform neutral container images. It turns out that flexibility can be used for additional artifacts.

This repository proposes a set of templates and guidance for registries to support new artifact types, and what a new artifact author would need to store and retrieve their artifact from an OCI compliant registry.

## Table of Contents

- [Working recommendation for supporting new artifact types](./mediaTypes.md)
- [Background for registries supporting new artifact types](#Background)
- [Leveraging Registries for Additional Artifacts](#Leveraging-Registries-for-Additional-Artifacts)
- [Requirements of registries to host new artifact types](./artifactTypeRequirements.md#Registry-Requirements-of-New-Artifact-Types)
- [Requirements of the new artifact type](./artifactTypeRequirements.md#Artifact-Requirements)
- [Extending `manifest.config.mediaType` to represent new artifacts](./mediaTypes.md)
- [Discussed approaches for storing new artifact types](./approaches.md)
- [OCI Index & Manifest considerations](#Manifest-and-Index)
## Background

Docker brought great usability to the evolving container ecosystem by providing end to end experiences. These experiences included a registry storing secured, layered, optional signed, images.

The experience to call `docker run [registry]/image:[version]` turns out to be productive and powerful, and not limited to just container images.

As new artifacts evolve, storing them in a registry for easy referencing turns out to be just as powerful. 

**Helm example**

```sh
helm upgrade myBlog demo42.azurecr.io/helm/wordpress:1.0 
    --reuse-values \
    --set wordpressBlogName=myBlog
```

**CNAB example**

```sh
duffle install myBlog demo42.azurecr.io/cnab/wordpress:1.0 
```

**Azure example**

```sh
az deployment create demo42.azurecr.io/arm/wordpress:1.0
```

**aws example**

```sh
aws ecs run-task --task-definition demo42.dkr.ecr.us-east-1.amazonaws.com/wordpress:1.0
```

## Leveraging Registries for Additional Artifacts

The major cloud vendors have implemented [docker/distribution](https://github.com/docker/distribution) to support the registry api, implementing their cloud specific storage and security semantics. Additional products and projects like [Codefresh](https://codefresh.io/), [JFrog](https://jfrog.com/), [Harbor](https://goharbor.io/) have built experiences around [docker/distribution](https://github.com/docker/distribution).

With the volume of development and production activity, these registries are hardened, secured & highly scalable and available.

By evolving registries to support additional artifact types, customers and cloud vendors can leverage their existing registry investments.

![](
https://stevelaskerblog.files.wordpress.com/2019/01/devprodenvironments-2.png?w=1024)

## Understanding Artifact Types

If we assume registries can host various artifact types, the next set of question include:

- How does one reason over the various artifact types in a registry?
- How would a registry listing represent the different artifact types? 
  - Can a registry show an icon and/or a short text identifier? 
- If a registry listings wishes to provide optimized action points, such as gestures to deploy Helm Chart, ecs task, or deploying an image directly to a container service; how would the UI know which actions to surface on which artifact types?
- How would vulnerability scanners know how to scan the various artifacts? Would scanners perform different scanning routines, based on the artifact type? How do they know the type?

## Registry Listing

While tools should have knowledge of the artifacts they work with, so should a registry. By providing information on the artifact, registries can display artifacts, with context and details.


**Registry:** `demo42.azurecr.io`

| artifact reference | icon | type | actions|
|-|-|-|-|
| `samples/image/hello-world:1.0` |![](./images/oci-container.png)| container image | `docker run ...` |
| `samples/helm/hello-world:1.0` |![](./images/helm.png)|  helm chart | `helm install ...` |
| `samples/cnab/hello-world:1.0` |![](./images/cnab.jpg)| CNAB | `duffle install ...` |
| `samples/arm/hello-world:1.0` |![](./images/arm.png)| arm | `az deployment create ...` |
| `samples/ecs-task/hello-world:1.0` |![](./images/ecs.png)| ecs-task | `aws ecs create-service ...` |


