# manifest.config.mediaTypes 

Artifact tools will need the ability to query their specific artifacts from a registry. In addition, registry vendors will want to understand the different artifact types they present to their users, providing additional information about the artifact. Including, the ability to launch the associated tool.

To uniquely identify artifact types within a registry, the `manifest.config.mediaType` will support additional `mediaTypes`. 


## Media Types

In this proposal, new `mediaTypes` are supported by the [`manifest.config` property descriptor](https://github.com/opencontainers/image-spec/blob/master/manifest.md#image-manifest-property-descriptions)

By supporting new `manifest.config.mediaTypes`, registry tools will understand the types stored. 
A simplified example of a registry listing might look like:

| artifact reference | icon | type | actions|
|-|-|-|-|
| `samples/image/hello-world:1.0` |![](./images/oci-container.png)| container image | `docker run ...` |
| `samples/image/hello-world-doc:1.0`|![](./images/doc.png)|doc| `markdown`|
| `samples/helm/hello-world:1.0` |![](./images/helm.png)|  helm chart | `helm install ...` |
| `samples/cnab/hello-world:1.0` |![](./images/cnab.jpg)| CNAB | `duffle install ...` |
| `samples/arm/hello-world:1.0` |![](./images/arm.png)| arm | `az deployment create ...` |
| `samples/ecs-task/hello-world:1.0` |![](./images/ecs.png)| ecs-task | `aws ecs create-service ...` |

An example CLI based registry listing:
```sh
az acr repository show-tags --name demo42 --repository helm/hello-world

Repository: helm/hello-world
Tag         Type
------------------
1.0-helm    helm
```

## Parsing `manifest.config.mediaType`
The [Config Descriptor in the OCI Image spec](https://github.com/opencontainers/image-spec/blob/master/manifest.md#image-manifest-property-descriptions) identifies the `mediaType` MUST support the `application/vnd.oci.image.config.v1+json` config. 

- **`config`** *[descriptor](descriptor.md)*

    This REQUIRED property references a configuration object for a container, by digest.
    Beyond the [descriptor requirements](descriptor.md#properties), the value has the following additional restrictions:

    - **`mediaType`** *string*

        This [descriptor property](descriptor.md#properties) has additional restrictions for `config`.
        Implementations MUST support at least the following media types:

        - [`application/vnd.oci.image.config.v1+json`](https://github.com/opencontainers/image-spec/blob/master/config.md)

        Manifests concerned with portability SHOULD use one of the above media types.

Registry owners MAY support additional `manifest.config.mediaType`s. 
When additional a new `mediaType` is encountered, the parsing of the config blob is open to the new `mediaType` to define. 

The options are:

- [`application/vnd.oci.image.config.v1+json`](https://github.com/opencontainers/image-spec/blob/master/config.md)
- None
- New schemas required by the new `mediaType`

## Layer mediaTypes

Each new artifact will define how they persist their layers. OCI Images have ordinal layers, supporting overlaying of files. Other artifact types may use layers to represent different objects. For instance, Helm might support a layer for the helm chart, with another layer for the values.

The [layer mediaType descriptor in the OCI Image spec](https://github.com/opencontainers/image-spec/blob/master/manifest.md#image-manifest-property-descriptions) identifies the `mediaType` MUST support one of the existing layer formats. 

- **`layers`** *array of objects*

    Each item in the array MUST be a [descriptor](descriptor.md).
    The array MUST have the base layer at index 0.
    Subsequent layers MUST then follow in stack order (i.e. from `layers[0]` to `layers[len(layers)-1]`).
    The final filesystem layout MUST match the result of [applying](layer.md#applying-changesets) the layers to an empty directory.
    The [ownership, mode, and other attributes](layer.md#file-attributes) of the initial empty directory are unspecified.

    Beyond the [descriptor requirements](descriptor.md#properties), the value has the following additional restrictions:

    - **`mediaType`** *string*

        This [descriptor property](descriptor.md#properties) has additional restrictions for `layers[]`.
        Implementations MUST support at least the following media types:

        - [`application/vnd.oci.image.layer.v1.tar`](layer.md)
        - [`application/vnd.oci.image.layer.v1.tar+gzip`](layer.md#gzip-media-types)
        - [`application/vnd.oci.image.layer.nondistributable.v1.tar`](layer.md#non-distributable-layers)
        - [`application/vnd.oci.image.layer.nondistributable.v1.tar+gzip`](layer.md#gzip-media-types)

        Manifests concerned with portability SHOULD use one of the above media types.

        Entries in this field will frequently use the `+gzip` types.

Each artifact author can extend these mediaTypes to identify their usage. 

A registry need not parse the `layer.mediaType`, as this is information processed by tooling that understands the specific artifact, providing it ownership of how layers are represented.

## Example Manifests

For illustrative purposes, Image, Helm and CNAB are expressed below. 

**Note:** While MUST will be included in the Helm and CNAB examples, these are just examples of what these specs *MAY* look like. The purpose of this proposal is to provide groups the freedom to define their unique artifact representation using `manifest.config.mediaTypes` and `layer.mediaTypes`


## OCI Image Index
As a baseline reference; the image index is a higher-level manifest which points to specific image manifests, ideal for one or more platforms. 

The proposal does not account for a new typed index. The proposal focuses on manifest being the unique artifact type.

**OCI Image Index example**

`docker run demo42.azurecr.io/samples/images/wordpress:0.1.0`
```json
{
  "schemaVersion": 2,
  "mediaType": "application/vnd.oci.image.index.v1+json",
  "manifests": [
    {
      "mediaType": "application/vnd.oci.image.manifest.v1+json",
      "size": 4288,
      "digest": "sha256:10b995d6204131069af3e4f00dc1d3758d517a5edb29e5757d3c2858d5613127",
      "platform": {
        "architecture": "amd64",
        "os": "linux"
      }
    },
    {
      "mediaType": "application/vnd.oci.image.manifest.v1+json",
      "size": 4288,
      "digest": "sha256:33c5d954c76495826126178ded2113d1ada0a1ea70f95b65f866312462c6e957",
      "platform": {
        "architecture": "arm",
        "os": "linux",
        "variant": "v5"
      }
    },
```

**OCI Image Manifest example**

```json
{
  "schemaVersion": 2,
  "mediaType": "application/vnd.oci.image.manifest.v1+json",
  "config": {
    "mediaType": "application/vnd.oci.image.config.v1+json",
    "size": 16078,
    "digest": "sha256:bd698aa18aa02a2f083292b9448130a3afaa9a3e5fba24fc0aef7845c336b8ad"
  },
  "layers": [
    {
      "mediaType": "application/vnd.oci.image.layer.v1.tar+gzip",
      "size": 23133155,
      "digest": "sha256:9a1a13172ed974323f7c35153e8b23b8fa1c85355b6b26cc3127e640e45ef0aa"
    },
    ...
```
> See [/container-image/readme.md](./container-image/readme.md) for more details.

## Helm Chart
> **Note:** *this is just an illustrative example of mediaTypes.*

> The persistance format has yet to be decided by the Helm community, the following represents what a Helm chart *could* be expressed as.

Unlike OCI Image manifests, Helm charts use layers to represent different elements of a chart. The files that make up the chart are separated from the values file *(parameter values)*. By separating values, multiple deployments of the same chart, only differentiated by the values, may be tracked and evaluated by new Helm tools.


The following represent `mediaTypes` used to represent manifests and layers of a Helm chart. The registry need not know of the `artifactTypes` for persisting the object. Rather, these are used by tooling specific to Helm, including other tools that may process the Helm object (eg: compliance scanners)


| mediaType | usage |
|-|-|
|`application/vnd.cncf.helm.config.v3`|Helm Config|
|`application/vnd.cncf.helm.chart.layer.v3`|Helm Chart archive layer|
|`application/vnd.cncf.helm.values.layer.v3`|Helm Values layer |

**Helm manifest example**

`helm install demo42.azurecr.io/samples/helm/wordpress:0.1.0`

```json
{
  "schemaVersion": 2,
  "mediaType": "application/vnd.oci.image.manifest.v1+json",
  "config": {
    "mediaType": "application/vnd.cncf.helm.config.v3+json",
    "size": 7023,
    "digest": "sha256:b5b2b2c507a0944348e0303114d8d93aaaa081732b86451d9bce1f432a537bc7",
    "annotations": {
      "io.cncf.helm.appVersion": "0.1.0"
    }
  },
  "layers": [
    {
      "mediaType": "application/vnd.cncf.helm.chart.layer.v3.tar+gzip",
      "size": 3155,
      "digest": "sha256:9a1a13172ed974323f7c35153e8b23b8fa1c85355b6b26cc3127e640e45ef0aa"
    },    
    {
      "mediaType": "application/vnd.cncf.helm.values.layer.v3+tar",
      "size": 3155,
      "digest": "sha256:9a1a13172ed974323f7c35153e8b23b8fa1c85355b6b26cc3127e640e45ef0aa"
    }

  ]
}
```

## CNAB
> **Note:** *this is just an illustrative example of the power of decoupling artifactTypes from mediaTypes, allowing reuse of existing artifacts*

> The persistance format has yet to be decided by the CNAB community, the following represents what a CNAB *could* be expressed as.


CNABs are interesting as they aren't a specific artifact in a registry, rather they may reference other artifacts in a registry. A CNAB can reference an invocation image, a Helm Chart, or several other images that may be deployed. It may also reference arm or cloud formation templates to establish infrastructure.

CNABs adds `mediaTypes` representing the `cnab.config`. This config layer is used to identify the invocation image and `cnab.component` which represents objects that are otherwise unknown to registries. A `cnab.config` may indicate whether the bundle uses an oci-image as it's invocation image, or another artifact that may be represented as a `cnab.component`. To support configurations unique to CNAB, CNAB implements it's own config schema, used by it's tooling.

In the example below, the existing `application/vnd.oci.image.manifest.v1+json` type is used to represent invocation images. While `application/vnd.deislabs.cnab.component.v1` is used to represent new opaqe types, unique to CNAB. 


| mediaType | usage |
|-|-|
|`application/vnd.deislabs.cnab.config.v1`|CNAB Config|
|`application/vnd.deislabs.cnab.component.v1`|CNAB component|


**CNAB example, using Duffle**

`duffle install myblog demo42.azurecr.io/samples/cnab/wordpress:0.1.0`
```json
{
  "schemaVersion": 2,
  "mediaType": "application/vnd.oci.image.manifest.v1+json",
  "config": {
    "manifests": [
      {
        "mediaType": "application/vnd.deislabs.cnab.config.v1+json",
        "size": 4288,
        "digest": "sha256:10b995d6204131069af3e4f00dc1d3758d517a5edb29e5757d3c2858d5613127"
      }
    ]
  },
  "layers": [
    {
      "mediaType": "application/vnd.oci.image.index.v1+json",
      "size": 4288,
      "digest": "sha256:10b995d6204131069af3e4f00dc1d3758d517a5edb29e5757d3c2858d5613127"
    },
    {
      "mediaType": "application/vnd.oci.image.manifest.v1+json",
      "size": 93288,
      "digest": "sha256:10b995d6204131069af3e4f00dc1d3758d517a5edb29e5757d3c2858d5613127"
    },
    {
      "mediaType": "application/vnd.microsoft.arm.template.v1+json",
      "size": 7023,
      "digest": "sha256:b5b2b2c507a0944348e0303114d8d93aaaa081732b86451d9bce1f432a537bc7"
    },
    {
      "mediaType": "application/vnd.aws.ecs.task.v1+json",
      "size": 2828,
      "digest": "sha256:4a2f7cd2e53307d7e9018b24f7ea9e3b9bad260f86359912707c5cb80aafa60b"
    },
    {
      "mediaType": "application/vnd.deislabs.cnab.component.v1.tar+gzip",
      "size": 7023,
      "digest": "sha256:b5b2b2c507a0944348e0303114d8d93aaaa081732b86451d9bce1f432a537bc7"
    }
  ]
}
```
